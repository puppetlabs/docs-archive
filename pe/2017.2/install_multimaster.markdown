---
layout: default
title: "Compile master installation"
canonical: "/pe/latest/install_multimaster.html"
---


As your infrastructure scales up to 4000 nodes and beyond, add load-balanced compile masters to your monolithic installation to increase the number of agents you can manage. Each compile master increases capacity by 1500 to 3000 nodes, until you exhaust the capacity of PuppetDB or the console, which run on the master of masters (MoM).

## How compile masters work

As your infrastructure grows beyond 4000 managed nodes, a single Puppet master won't be able to process all the requests and compile all the code for those Puppet agents. Scale your infrastructure by adding compile masters to share the workload and compile catalogs faster. Compile masters perform many of the same functions as a Puppet master: they run file sync, contain a Puppet Server, and can host `pe_repo`. When you deploy compile masters, the main Puppet master is known as the master of masters (MoM).

> **Tip:** See the [PE hardware recommendations](./sys_req_hw.html) for guidance on base installation types and recommended hardware for each.

> **Important:** Compile masters must run the [same OS major version, platform, and architecture](./sys_req_os.html#puppet-master-platforms) as the MoM.

### Components and services running on compile masters

All compile masters contain a Puppet Server and a [file sync](./cmgmt_filesync.html) client. When triggered by a web endpoint, file sync takes changes from the working directory on the MoM and deploys the code to a live code directory. File sync then deploys that code onto all your compile masters, ensuring that all masters in a multi-master configuration are kept in sync. By default, compile masters check for code updates every five seconds.

The certificate authority (CA) service is disabled on compile masters. A proxy service running on the compile master's Puppet Server directs CA requests to the MoM, which hosts the CA in default installations.

Compile masters also have:

- the MCollective client, `peadmin`
- PE's repository for agent installation, `pe_repo`
- the controller profile used with PE client tools
- PCP brokers to enable orchestrator scale

Logs for compile masters are at `/var/log/puppetlabs/puppetserver/`.

Logs for PCP brokers on compile masters are at `/var/log/puppetlabs/puppetserver/pcp-broker.log`.

## Install compile masters

When you install a compile master, you first install a Puppet agent and then classify that agent as a compile master.

Before you begin:

Review these procedures before you start. Performing these steps out of order can cause problems for your configuration. In addition, note the following about these steps:

- These instructions assume you've already installed a [split](./install_pe_split.html) or [monolithic](./install_pe_mono.html) PE deployment.
- All servers must run the same OS and architecture.
- In these instructions, the following hostnames are used. Replace them with the corresponding hostnames in your infrastructure:
   - **Puppet master/CA server (MoM)**: `MASTER.EXAMPLE.COM`
   - **PE console**: `CONSOLE.EXAMPLE.COM`
   - **PuppetDB**: `PUPPETDB.EXAMPLE.COM`
   - **Compile master**: `COMPILE.MASTER.EXAMPLE.COM`
- The machine that will become your compile master should **not** already have a Puppet agent installed.


1. To install and configure the Puppet agent on `COMPILE.MASTER.EXAMPLE.COM`, SSH into `COMPILE.MASTER.EXAMPLE.COM` and run the following command:

   ~~~
   `curl -k https://<MASTER.EXAMPLE.COM>:8140/packages/current/install.bash | sudo bash -s main:dns_alt_names=<COMMA-SEPARATED LIST OF ALT NAMES FOR THE COMPILE MASTER>`
   ~~~

   >**Note:** Set the `dns_alt_names` value to a comma-separated list of any alternative names that Puppet agents will use to connect to compile masters. The installation uses "puppet" by default.


2. From the command line of `MASTER.EXAMPLE.COM`, run the following command:

   ~~~
   puppet cert --allow-dns-alt-names sign COMPILE.MASTER.EXAMPLE.COM
   ~~~

   >**Note**: You cannot use the console to sign certs for nodes with DNS alt names.

3. From the command line on `COMPILE.MASTER.EXAMPLE.COM`, run `puppet agent -t`.

4. In the console, classify `COMPILE.MASTER.EXAMPLE.COM` so that it can function as a Puppet master and proxy requests to the PE certificate authority.

   a. In the console, click **Classification**, and in the **PE Infrastructure** node group, select the **PE Master** node group.

   b. From the __Certname__ section, in the __Node name__ field, enter `COMPILE.MASTER.EXAMPLE.COM`.

   c. Click __Pin node__, and commit changes.

5. Configure `pe_repo` to send agent install requests to the load balancer.

   >**Note:** Specifics on how to configure a load balancer fall outside the scope of this document, but we've provided a list of things to consider in [Using load balancers with compile masters](./install_lei_load.html).

   a. From the console, click __Classification__, and in the __PE Infrastructure__ node group, select the __PE Master__ group.

   b. In the __PE Master__ group, click the __Classes__ tab, and find the __pe_repo__ class.

   c. From the __Parameter__ drop-down list, select __compile_master_pool_address__.

   d. In the __Value__ field, enter the address your load balancer resolves to (for example, `LOADBALANCER.EXAMPLE.COM`).

   e. Click __Add parameter__ and then __Commit change__.

6. [Run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes) on the following nodes in the order indicated.

   >**Important**: The following Puppet runs **MUST** be done in the order listed in the following choices. In both cases, wait for the run to finish on the the first node before moving on to the next.

   - **For a monolithic installation:**
      1. `COMPILE.MASTER.EXAMPLE.COM`
      2. `MASTER.EXAMPLE.COM`

   - **For a split installation:**
      1. `COMPILE.MASTER.EXAMPLE.COM`
      2. `PUPPETDB.EXAMPLE.COM`
      3. `CONSOLE.EXAMPLE.COM`
      4. `MASTER.EXAMPLE.COM`



## Configure compile masters for orchestrator scale

As your PE installation grows, you can scale orchestrator jobs by configuring connections between your agents, load balancers, and compile masters.

The Puppet orchestrator [communicates with PCP brokers on compile masters](./orchestrator_intro.html#puppet-orchestrator-technical-overview) over port 8143, and sends job-related messages to the brokers, which are then relayed by the brokers to PXP agents. As you add compile masters, you also scale the number of PCP brokers that can send orchestration messages to agents.

When adding compile masters, you should load balance PXP agent connections to the PCP brokers running on them. PXP agents connect to PCP brokers running on compile masters over port 8142.

PCP brokers are built on websockets and require many persistent connections. If you're not [using HTTP health checks](#using-health-checks), use a round robin or random load balancing algorithm for PXP agent connections to PCP brokers, as PCP brokers don't operate independent of the orchestrator and will isolate themselves if they become disconnected. You can check connections for error states by using the [`/status/v1/simple`](./status_api.html#get-statusv1simple) endpoint. Status checks on compile masters must be sent to port 8140.

You must also configure your load balancer to avoid closing long-lived connections that have little traffic. If you're using the HAproxy module, set the `timeout tunnel` to `15m` since PCP brokers will disconnect inactive connections after 15 minutes. Status checks on compile masters must be sent to port 8140.

>Note: Reconnecting agents after a service restart or outage is faster when you use rate limiting. For HAproxy, that's `rate-limit sessions`. We recommend limiting to approximately 5-10 x (number of compile masters) x (number of CPU cores per compile master) per second. 


>Prerequisite: This task assumes you've already installed the compile masters and [load balancers](./install_lei_load.html#load-balancing-for-orchestration-scale) you need. Additionally, if you've used DNS alt names for your loadbalancers, add those to the Puppet master.

1. Ensure port 8143 is open on the master of masters or on any workstations used to run orchestrator jobs. 
2. Configure the [**PE Infrastructure Agent**](./console_classes_groups_preconfigured_groups.html#the-pe-infrastructure-agent-node-group) group so nodes in that group to connect to the MoM.

   a. In the console, click **Classification**, and in the **PE Infrastructure** group, select the **PE Agent** group.

   b. If you manage your load balancers with Puppet agents, pin them to this group in the **Rules** tab.

   c. On the **Classes** tab, find the `puppet_enterprise::profile::agent` class and add the following parameter and value:

   Parameter | Value
   ----------|------
   `pcp_broker_list` | A JSON list including the  hostname for your MoM. If you have a HA Replica it should be included after the MoM. <br> Hostnames must include port `8142`. For example, <br> `["MASTER.EXAMPLE.COM:8142"]`.

   d. Click **Add paramater**.

   e. Remove any values set for `puppet_enterprise::profile::agent::pcp_broker_ws_uris`.

   f. Commit changes.

   g. Run Puppet on all agents classified into the PE Infrastructure Agent group.

   This Puppet run should not change pxp-agent configuration. If you have HA configured and haven't already pinned your loadbalancer to the **PE Infrastructure Agent** group this will configure your loadbalancer to compile catalogs on the MoM.


3. Configure **PE Agent** nodes to connect to your loadbalancer

    a. In the console, click **Classification**, and in the **PE Infrastructure** group, select the **PE Agent** group.

    b. On the **Classes** tab, find the `puppet_enterprise::profile::agent` class and add the following parameter and value:

   Parameter | Value
   ----------|------
   `pcp_broker_list` | A JSON list of hostnames for your load balancers. <br> Hostnames must include port `8142`. For example, <br> `["LOADBALANCER1.EXAMPLE.COM:8142", "LOADBALANCER1.EXAMPLE.COM:8142"]`.

   c. Click **Add paramater**.

   d. Remove any values set for `puppet_enterprise::profile::agent::pcp_broker_ws_uris`.

   e. Commit changes.

   f. Run Puppet on the master.

   g. Run Puppet on all agents, or install new agents.

   This Puppet run will configure the pxp-agents to connect to the load balancer.

