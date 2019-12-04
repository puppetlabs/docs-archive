# Installing compile masters

As your Puppet Enterprise infrastructure scales up to 4,000 nodes and beyond, add load-balanced compile masters to your monolithic installation to increase the number of agents you can manage.

Each compile master increases capacity by 1,500 to 3,000 nodes, until you exhaust the capacity of PuppetDB or the console.

## How compile masters work

A single master can't efficiently process requests and compile code for more than 4,000 nodes. When you reach this scale, expand your infrastructure by adding compile masters to share the workload and compile catalogs faster.

In a configuration that uses compile masters, the main master is known as the master of masters \(MoM\).

**Important:** Compile masters must run the same OS major version, platform, and architecture as the MoM.

### Components and services running on compile masters

All compile masters contain a Puppet Server and a file sync client.

When triggered by a web endpoint, file sync takes changes from the working directory on the MoM and deploys the code to a live code directory. File sync then deploys that code to all your compile masters, ensuring that all masters in a multi-master configuration remain in sync. By default, compile masters check for code updates every five seconds.

The certificate authority \(CA\) service is disabled on compile masters. A proxy service running on the compile master Puppet Server directs CA requests to the MoM, which hosts the CA in default installations.

Compile masters also have:

-   The MCollective client, `peadmin`
-   The repository for agent installation, `pe_repo`
-   The controller profile used with PE client tools
-   Puppet Communications Protocol \(PCP\) brokers to enable orchestrator scale

Logs for compile masters are located at `/var/log/puppetlabs/puppetserver/`.

Logs for PCP brokers on compile masters are located at `/var/log/puppetlabs/puppetserver/pcp-broker.log`.

## Install compile masters

To install a compile master, you first install an agent and then classify that agent as a compile master.

### Procedure

1.  SSH into the node that you want to make a compile master and install the agent:

    ```
       `curl -k https://<MoM_HOSTNAME>:8140/packages/current/install.bash | sudo bash -s main:dns_alt_names=<COMMA-SEPARATED LIST OF ALT NAMES FOR THE COMPILE MASTER>`
    ```

    **Note:** Set the `dns_alt_names` value to a comma-separated list of any alternative names that agents use to connect to compile masters. The installation uses `puppet` by default.

2.  From the master of masters, sign the compile master's certificate:

    `puppet cert --allow-dns-alt-names sign <COMPILE_MASTER_HOSTNAME>`

    **Note:** You can't use the console to sign certs for nodes with DNS alt names.

3.  From the compile master, run Puppet: `puppet agent -t`

4.  In the console, pin the compile master node to the **PE Master** node group.

    1.  In the console, click **Classification**, and in the **PE Infrastructure** group, select the **PE Master** group.

    2.  Enter the **Certname** for the compile master, click **Pin node**, and then commit changes.

5.  Configure `pe_repo` to send agent install requests to the load balancer.

    **Note:** Updating the `compile_master_pool_address` in Hiera or setting it as configuration data in the console, as documented in this step, also updates the agent install script URL displayed in the console.

    1.  In the console, click **Classification**, and in the **PE Infrastructure** group, select the **PE Master** group.

    2.  Select the **Configuration** tab, and in the **Data** section, specify parameters:

        |**Parameter**|**Value**|
        |-------------|---------|
        |**compile\_master\_pool\_address**|Load balancer hostname.|

    3.  Click **Add data** and commit changes.

6.  Run Puppet on infrastructure nodes in the order required for your configuration.

    **Monolithic configurations**

    Run Puppet in this order:

    -   Compile master

    -   Master of masters

    **Split configurations**

    Run Puppet in this order:

    -   Compile master

    -   PuppetDB node

    -   Console node

    -   Master of masters


## Configure compile masters for orchestrator scale

As your installation grows, you can scale orchestrator jobs by configuring connections between your agents, load balancers, and compile masters.

### Before you begin

Install compile masters and load balancers. If you need DNS altnames for your load balancers, add them to the master.

### About this task

The orchestrator communicates with PCP brokers on compile masters over port 8143, and sends job-related messages to the brokers, which are then relayed by the brokers to Puppet Execution Protocol \(PXP\) agents. As you add compile masters, you also scale the number of PCP brokers that can send orchestration messages to agents.

When adding compile masters, you should load balance PXP agent connections to the PCP brokers running on them. PXP agents connect to PCP brokers running on compile masters over port 8142. Status checks on compile masters must be sent to port 8140, using `https://<hostname>:8140/status/v1/simple`.

Tasks also require a configuration. If you want to download tasks from a Puppet master, you configure `puppet_enterprise::profile::agent::master_uris` to a list of load balancer host names.

### Procedure

1.  Ensure port 8143 is open on the master of masters or on any workstations used to run orchestrator jobs.

2.  Configure infrastructure agents to connect to the MoM.

    1.  In the console, click **Classification**, and in the **PE Infrastructure** group, select the **PE Infrastructure Agent** group.

    2.  If you manage your load balancers with agents, on the **Rules** tab, pin load balancers to the group.

    3.  On the **Configuration** tab, find the **puppet\_enterprise::profile::agent** class and specify parameters:

        |**Parameter**|**Value**|
        |-------------|---------|
        |**pcp\_broker\_list**|JSON list including the hostname for your MoM. If you have an HA replica, include it after the MoM. Hostnames must include port 8142, for example \["MASTER.EXAMPLE.COM:8142"\].|
        |**master\_uris**|If you want to use tasks, provide a list of load balancer host names, for example, \["MASTER.EXAMPLE.COM"\]. This setting infers port 8140 unless you specify with host:port. |

        **Note:** If you’re using the PE module and managing server list with the `puppet infrastructure`command, then you don’t need to set `master_uris`.

    4.  Remove any values set for **pcp\_broker\_ws\_uris**.

    5.  Commit changes.

    6.  Run Puppet on all agents classified into the **PE Infrastructure Agent** group. 

        This Puppet run doesn't change PXP agent configuration. If you have high availability configured and haven’t already pinned your load balancer to the **PE Infrastructure Agent** group, the Puppet run configures your load balancer to compile catalogs on the MoM.

3.  Configure agents to connect to your load balancer.

    1.  In the console, click **Classification**, and in the **PE Infrastructure** group, select the **PE Agent** group.

    2.  On the **Configuration** tab, find the **puppet\_enterprise::profile::agent** class and specify parameters:

        |**Parameter**|**Value**|
        |-------------|---------|
        |**pcp\_broker\_list**|JSON list including the hostname for your load balancers. Hostnames must include port 8142, for example \["LOADBALANCER1.EXAMPLE.COM:8142","LOADBALANCER2.EXAMPLE.COM:8142"\]|

    3.  Remove any values set for **pcp\_broker\_ws\_uris**.

    4.  Commit changes.

    5.  Run Puppet on the master, then run Puppet on all agents, or install new agents.

        This Puppet run configures PXP agents to connect to the load balancer.


## Using load balancers with compile masters

In a large environment installation \(LEI\), agent requests are distributed among a pool of compile masters in order to increase the environment's total capacity.

You can configure a simple TCP load balancer to route traffic between agents and compile masters on port 8140.

Specifics on how to configure a load balancer infrastructure falls outside the scope of this document, but examples of how to leverage `haproxy` for this purpose can be found in the HAproxy module documentation.

### Load balancing for orchestration scale

PCP brokers run on compile masters and connect to PXP agents over port 8142. PCP brokers are built on websockets and require many persistent connections. If you're not using HTTP health checks, we recommend using a round robin or random load balancing algorithm for PXP agent connections to PCP brokers, because PCP brokers don't operate independent of the orchestrator and will isolate themselves if they become disconnected. You can check connections with the `/status/v1/simple` endpoint for an error state.

You must also configure your load balancer to avoid closing long-lived connections that have little traffic. In the HAproxy module, you can set the `timeout tunnel` to `15m` since PCP brokers disconnect inactive connections after 15 minutes.

### Using health checks

The Puppet REST API exposes a status endpoint that can be leveraged from a load balancer health check to ensure that unhealthy hosts do not receive agent requests from the load balancer.

The master service responds to unauthenticated HTTP GET requests issued to `https://<hostname>:8140/status/v1/simple`. The API responds with an HTTP 200 status code if the service is healthy.

If your load balancer doesn't support HTTP health checks, a simpler alternative is to check that the host is listening for TCP connections on port 8140. This ensures that requests aren't forwarded to an unreachable instance of the master, but it does not guarantee that a host is pulled out of rotation if it's deemed unhealthy, or if the service listening on port 8140 is not a service related to Puppet.

### Optimizing workload distribution

Due to the diverse nature of the network communications between the agent and the master, we recommend that you implement a load balancing algorithm that distributes traffic between compile masters based on the number of open connections. Load balancers often refer to this strategy as "balancing by least connections."

### Configuring `pe_repo` for agent installation

When installing a new agent from a load balanced pool of compile masters, the agent configuration points to whichever compile master handled the request, instead of the load balancer itself. To ensure agents take advantage of pooled masters, specify the `pe_repo::compile_master_pool_address` in the **PE Master** node group.

**Related topics**  


[GET /status/v1/simple](status_api_plaintext_endpoints.md#)

