---
layout: default
title: "Compile Master Installation"
canonical: "/pe/latest/install_multimaster.html"
---

As your infrastructure scales (4000+ nodes), you can add load-balanced compile masters to your monolithic installation to increase the amount of agents you can manage. Each compile master increases capacity by approximately 1500 - 3000 nodes, until you exhaust the capacity of PuppetDB or the PE console, which run on the Master of masters (MoM).

## How compile masters work

The following provides an overview of how compile masters work and relevant details about them.

As your infrastructure grows beyond 2000 managed nodes, a single Puppet master most likely won't be able to process all the requests and compile all the code for those Puppet agents. You can scale your infrastructure by adding compile masters to share the workload and provide quicker, more efficient compilation times. Compile masters perform many of the same functions as a Puppet master: they run file sync, contain a Puppet Server, and can host `pe_repo`. When you deploy compile masters, the main Puppet master is known as the master of masters (MoM).

> **Tip:** See the [PE hardware recommendations](./sys_req_hw.html) for guidance on base installation types and recommended hardware for each.

> **Important:** Compile masters must run the [same OS major version, platform, and architecture](./sys_req_os.html#puppet-master-platforms) as the MoM.

### Components and services running on compile masters

All compile masters contain a Puppet Server and a [file sync](./cmgmt_filesync.html) client. When triggered by a web endpoint, file sync takes changes from your working directory on your Master of Masters (MoM) and deploys the code to a live code directory. File sync then automatically deploys that code onto all your compile masters, ensuring that all masters in a multi master configuration are kept in sync. By default, compile masters check for code updates every five seconds.

The CA service is disabled on compile masters. A proxy service running on the compile master's Puppet Server directs CA requests to the MoM, which hosts the CA in default installations. 

Compile masters also have:

- `peadmin` (the MCollective client)
- `pe_repo` (PE's repo for agent installation)
- the controller profile (used in conjunction with PE client tools)

Compile master logs are kept at `/var/log/puppetlabs/puppetserver/`. 

## Install compile masters

When you install a compile master, you first install a Puppet agent and then classify that agent as a compile master. 

Before you begin:

Review these procedures before beginning, as performing these steps out of order can cause problems for your configurations. In addition, note the following about these steps:

- It's assumed you've already installed a [split](./install_pe_split.html) or [monolithic](./install_pe_mono.html) PE deployment.
- It’s assumed all servers are running the same OS and architecture.
- In these procedures, the following hostnames are used, but you will need to replace them to match the hostnames in your infrastructure:
   - **Puppet master/CA server (MoM)**: `MASTER.EXAMPLE.COM`.
   - **PE console**: `CONSOLE.EXAMPLE.COM`.
   - **PuppetDB**: `PUPPETDB.EXAMPLE.COM`.
   - **Compile master**: `COMPILE.MASTER.EXAMPLE.COM`.
- The machine that will become your compile master should NOT already have a Puppet agent installed.


1. SSH into `COMPILE.MASTER.EXAMPLE.COM` and run the following command:

   ~~~
   `curl -k https://<MASTER.EXAMPLE.COM>:8140/packages/current/install.bash | sudo bash -s main:dns_alt_names=<COMMA-SEPARATED LIST OF ALT NAMES FOR THE COMPILE MASTER>`
   ~~~
   
   >**Note:** The `dns_alt_names` value should be set to a comma-separated list of any alternative names that may be used by Puppet agents to connect to the master. The installation uses "puppet" by default.
   
   This installs and configures the Puppet agent on `COMPILE.MASTER.EXAMPLE.COM`.
   
2. From the command line of `MASTER.EXAMPLE.COM`, run the following command:

   ~~~
   puppet cert --allow-dns-alt-names sign COMPILE.MASTER.EXAMPLE.COM
   ~~~

   >**Note**: You cannot use the console to sign certs for nodes with DNS alt names.
   
3. From the command line on `COMPILE.MASTER.EXAMPLE.COM`, run `puppet agent -t`.

4. Use the PE console to classify `COMPILE.MASTER.EXAMPLE.COM` so that it can function as a Puppet master and proxy requests to the PE certificate authority.

   a. In the console, click **Nodes** > **Classification**, and in the **PE Infrastructure** node group, select the **PE Master** node group.
   
   b. From the __Certname__ section, in the __Node name__ field, enter `COMPILE.MASTER.EXAMPLE.COM`.
   
   c. Click __Pin node__, and commit changes.
   
5. Configure `pe_repo` to send agent install requests to the load balancer.

   >**Note:** Specifics on how to configure a load balancer fall outside the scope of this document, but we've provided a list of things to consider in [Using load balancers with compile masters](./install_lei_load.html).

   a. From the console, click __Nodes__ > __Classification__, and in the __PE Infrastructure__ node group, select the __PE Master__ group.
   
   b. In the __PE Master__ group, click the __Classes__ tab, and find the __pe_repo__ class.
   
   c. From the __Parameter__ drop-down list, select __compile_master_pool_address__.
   
   d. In the __Value__ field, enter the address your load balancer resolves to (for example, `LOADBALANCER.EXAMPLE.COM`).
   
   e. Click __Add parameter__ and then the __Commit change__ button.

6. [Run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes) on selected nodes. 

   >**Important**: The following Puppet runs **MUST** be done in the order listed in the following choices. Puppet has to be run on these nodes in this order for the compile master to be active as quickly as possible. 

   - **For a monolithic installation:**
      1. `COMPILE.MASTER.EXAMPLE.COM`
      2. `MASTER.EXAMPLE.COM`
    
   - **For a split installation:**
      1. `COMPILE.MASTER.EXAMPLE.COM`
      2. `PUPPETDB.EXAMPLE.COM`
      3. `CONSOLE.EXAMPLE.COM`
      4. `MASTER.EXAMPLE.COM`

   In both cases, you must wait for the run to finish on the the first node before moving on to the next.



