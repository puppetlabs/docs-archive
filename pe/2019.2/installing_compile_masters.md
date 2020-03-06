# Installing compilers

As your Puppet Enterprise infrastructure scales up to 4,000 nodes and beyond, add load-balanced compilers to your monolithic installation to increase the number of agents you can manage.

Each compiler increases capacity by 1,500 to 3,000 nodes, until you exhaust the capacity of PuppetDB or the console.

## How compilers work

A single master can process requests and compile code for up to 4,000 nodes. When you exceed this scale, expand your infrastructure by adding compilers to share the workload and compile catalogs faster.

**Important:** Compilers must run the same OS major version, platform, and architecture as the master.

Compilers act as PCP brokers, conveying messages between the orchestrator and Puppet Execution Protocol \(PXP\) agents. PXP agents connect to PCP brokers running on compilers over port 8142. Status checks on compilers must be sent to port 8140, using `https://<hostname>:8140/status/v1/simple`.

![](mono_compile_port_diagram.png)

### Components and services running on compilers

All compilers contain a Puppet Server and a file sync client.

When triggered by a web endpoint, file sync takes changes from the working directory on the master and deploys the code to a live code directory. File sync then deploys that code to all your compilers, ensuring that all masters in a multi-master configuration remain in sync. By default, compilers check for code updates every five seconds.

The certificate authority \(CA\) service is disabled on compilers. A proxy service running on the compiler Puppet Server directs CA requests to the master, which hosts the CA in default installations.

Compilers also have:

-   The repository for agent installation, `pe_repo`
-   The controller profile used with PE client tools
-   Puppet Communications Protocol \(PCP\) brokers to enable orchestrator scale

Logs for compilers are located at `/var/log/puppetlabs/puppetserver/`.

Logs for PCP brokers on compilers are located at `/var/log/puppetlabs/puppetserver/pcp-broker.log`.

## Using load balancers with compilers

When using more than one compiler, a load balancer can help distribute the load between the compilers and provide a level of redundancy.

Specifics on how to configure a load balancer infrastructure falls outside the scope of this document, but examples of how to leverage `haproxy` for this purpose can be found in the HAproxy module documentation.

### Load balancing

PCP brokers run on compilers and connect to PXP agents over port 8142. PCP brokers are built on websockets and require many persistent connections. If you're not using HTTP health checks, we recommend using a round robin or random load balancing algorithm for PXP agent connections to PCP brokers, because PCP brokers don't operate independent of the orchestrator and isolate themselves if they become disconnected. You can check connections with the `/status/v1/simple` endpoint for an error state.

You must also configure your load balancer to avoid closing long-lived connections that have little traffic. In the HAproxy module, you can set the `timeout tunnel` to `15m` because PCP brokers disconnect inactive connections after 15 minutes.

### Using health checks

The Puppet REST API exposes a status endpoint that can be leveraged from a load balancer health check to ensure that unhealthy hosts do not receive agent requests from the load balancer.

The master service responds to unauthenticated HTTP GET requests issued to `https://<hostname>:8140/status/v1/simple`. The API responds with an HTTP 200 status code if the service is healthy.

If your load balancer doesn't support HTTP health checks, a simpler alternative is to check that the host is listening for TCP connections on port 8140. This ensures that requests aren't forwarded to an unreachable instance of the master, but it does not guarantee that a host is pulled out of rotation if it's deemed unhealthy, or if the service listening on port 8140 is not a service related to Puppet.

### Optimizing workload distribution

Due to the diverse nature of the network communications between the agent and the master, we recommend that you implement a load balancing algorithm that distributes traffic between compilers based on the number of open connections. Load balancers often refer to this strategy as "balancing by least connections."

**Related information**  


[Firewall configuration for large installations with compilers](system_configuration.md#)

[GET /status/v1/simple](status_api_plaintext_endpoints.md#)

## Install compilers

To install a compiler, you first install an agent and then classify that agent as a compiler.

### Procedure

1.  SSH into the node that you want to make a compiler and install the agent:

    ```
    curl -k https://<MASTER_HOSTNAME>:8140/packages/current/install.bash | sudo bash -s main:dns_alt_names=<COMMA-SEPARATED LIST OF ALT NAMES FOR THE COMPILER>
    ```

    **Note:** Set the `dns_alt_names` value to a comma-separated list of any alternative names that agents use to connect to compilers. The installation uses `puppet` by default. If your `puppet.conf` file includes a `dns_alt_names` entry, you must include the `dns_alt_names` parameter.

2.  From the master, sign the compiler's certificate:

    `puppetserver ca sign --certname <COMPILER_HOSTNAME>`

3.  Pin the compiler node to the **PE Master** node group.

    1.  In the console, click **Classification**, and in the **PE Infrastructure** group, select the **PE Master** group.

    2.  Enter the **Certname** for the compiler, click **Pin node**, and then commit changes.

4.  From the compiler, run Puppet: `puppet agent -t`

5.  From the master, run Puppet: `puppet agent -t`


### What to do next

After installing compilers, you must configure them to appropriately route communication between your master and agent nodes.

## Configure compilers

Compilers must be configured to appropriately route communication between your master and agent nodes.

### Before you begin

-   Install compilers and load balancers.

-   If you need DNS altnames for your load balancers, add them to the master.

-   Ensure port 8143 is open on the master or on any workstations used to run orchestrator jobs.


### Procedure

1.  Configure `pe_repo::compile_master_pool_address` to send agent install requests to the load balancer.

    **Important:** If you have load balancers in multiple data centers, you must configure `compile_master_pool_address` using Hiera, instead of using configuration data in the console, as described in this step. Using either of these methods updates the agent install script URL displayed in the console.

    **Note:** If you are using a single compile master, configure `compile_master_pool_address` with the compile master's fully qualified domain name \(FQDN\).

    1.  In the console, click **Classification**, and in the **PE Infrastructure** group, select the **PE Master** group.

    2.  Select the **Configuration** tab, and in the `Data` section, in the **pe\_repo** class, specify parameters:

        |**Parameter**|**Value**|
        |-------------|---------|
        |**compile\_master\_pool\_address**|Load balancer hostname.|

    3.  Click **Add data** and commit changes.

2.  Run Puppet on the compiler, and then on the master.

3.  Configure infrastructure agents to connect orchestration agents to the master.

    1.  In the console, click **Classification**, and in the **PE Infrastructure** group, select the **PE Infrastructure Agent** group.

    2.  If you manage your load balancers with agents, on the **Rules** tab, pin load balancers to the group.

    3.  On the **Configuration** tab, find the **puppet\_enterprise::profile::agent** class and specify parameters:

        |**Parameter**|**Value**|
        |-------------|---------|
        |**pcp\_broker\_list**|JSON list including the hostname for your master. If you have an HA replica, include it after the master. Hostnames must include port 8142, for example \["MASTER.EXAMPLE.COM:8142"\].|
        |**master\_uris**|Provide the host name for your master, for example, \["https://MASTER.EXAMPLE.COM"\]. Uris must begin with `https://`. This setting assumes port 8140 unless you specify otherwise with `host:port`.|

    4.  Remove any values set for **pcp\_broker\_ws\_uris**.

    5.  Commit changes.

    6.  Run Puppet on all agents classified into the **PE Infrastructure Agent** group. 

        This Puppet run doesn't change PXP agent configuration. If you have high availability configured and haven’t already pinned your load balancer to the **PE Infrastructure Agent** group, the Puppet run configures your load balancer to compile catalogs on the master.

4.  Configure agents to connect orchestration agents to the load balancer.

    1.  In the console, click **Classification**, and in the **PE Infrastructure** group, select the **PE Agent** group.

    2.  On the **Configuration** tab, find the **puppet\_enterprise::profile::agent** class and specify parameters:

        |**Parameter**|**Value**|
        |-------------|---------|
        |**pcp\_broker\_list**|JSON list including the hostname for your load balancers. Hostnames must include port 8142, for example \["LOADBALANCER1.EXAMPLE.COM:8142","LOADBALANCER2.EXAMPLE.COM:8142"\]|
        |**master\_uris**|Provide a list of load balancer host names, for example, \["https://LOADBALANCER1.EXAMPLE.COM","https://LOADBALANCER2.EXAMPLE.COM"\]. Uris must begin with `https://`. This setting assumes port 8140 unless you specify otherwise with `host:port`.|

    3.  Remove any values set for **pcp\_broker\_ws\_uris**.

    4.  Commit changes.

    5.  Run Puppet on the master, then run Puppet on all agents, or install new agents.

        This Puppet run configures PXP agents to connect to the load balancer.


**Related information**  


[Firewall configuration for large installations with compilers](system_configuration.md#)

