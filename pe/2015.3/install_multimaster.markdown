---
layout: default
title: "Compile Master Installation"
canonical: "/pe/latest/install_multimaster.html"
---


The following guide provides instructions for adding compile masters to your PE infrastructure. 

## How compile masters work

As your infrastructure grows beyond 2000 managed nodes, a single Puppet master most likely won't be able to process all the requests and compile all the code for those Puppet agents. You can scale your infrastructure by adding compile masters to share the workload and provide quicker, more efficient compilation times. Compile masters perform many of the same functions as a Puppet master: they run file sync, contain a Puppet Server, and can host `pe_repo`. When you deploy compile masters, the main Puppet master is known as the master of masters (MoM).  

See the [PE hardware recommendations](./install_system_requirements.html#hardware-recommendations) for guidance on base installation types and recommended hardware for each.

### Components and services running on compile masters

All compile masters contain a Puppet Server and a file sync client.

If you classify your compile masters using the **PE Master** node group (as shown in these instructions), the compile masters will also have:

- `peadmin` (the MCollective client)
- `pe_repo` (PE's repo for agent installation)
- the controller profile (used in conjunction with PE client tools)

### Compile master logs

Compile master logs are kept at `/var/log/puppetlabs/puppetserver/`.
 
### Puppet code on compile masters

If you use file sync, all the Puppet code in the code directory on your MoM is distributed to all compile masters. By default, compile masters check for code updates every five seconds.    

### Supported OS platforms

Compile masters must run the [same OS platform and architecture](./install_system_requirements.html#puppet-master-platforms) as the MoM. 

### Compile masters and the Puppet certificate authority

The CA service is disabled on compile masters. A proxy service running on the compile master's Puppet Server directs CA requests to the MoM, which hosts the CA in default installations. 

## Install a compile master

Please be sure to review these procedures before beginning, as performing these steps out of order can cause problems for your configurations.

In addition, please note the following about this guide:

- It’s assumed all servers are running the same OS and architecture.
- In these procedures, the following hostnames are used, but you will need to replace them to match the hostnames in your infrastructure:
   - **Puppet master/CA server (MoM)**: `<MASTER.EXAMPLE.COM>`.
   - **PE console**: `<CONSOLE.EXAMPLE.COM>`.
   - **PuppetDB**: `<PUPPETDB.EXAMPLE.COM>`.
   - **Compile master**: `<COMPILE.MASTER.EXAMPLE.COM>`.

> **Note**: In this scenario, `<MASTER.EXAMPLE.COM>` functions as both the MoM and the CA server for this deployment.

> **Prerequisite**: You need to be able to resolve hostnames between machines.

## Step 1: Install Puppet Enterprise

In this step, you will install PE and install the MoM on `<MASTER.EXAMPLE.COM>`.

1. Follow the [split](./install_pe_split.html) or [monolithic](./install_pe_mono.html) installation documentation to install PE.
2. Log in to the console.

## Step 2: Install Compile Master Node

In this step, you will install the additional Puppet agent on `<COMPILE.MASTER.EXAMPLE.COM>`. You must perform this step to install the Puppet agent on the new compile master node.

**Warning**: This machine (e.g., the new compile master) should NOT already have a Puppet agent installed.

**To install the additional compile master agent**:

1. SSH into `<COMPILE.MASTER.EXAMPLE.COM>` and run `curl -k https://<MASTER.EXAMPLE.COM>:8140/packages/current/install.bash | sudo bash -s main:dns_alt_names=<COMMA-SEPARATED LIST OF ALT NAMES FOR THE PUPPET MASTER>`.

   **Note**: The `dns_alt_names` value should be set to a comma-separated list of any alternative names that may be used by Puppet agents to connect to the master. The installation uses "puppet" by default.

   This will install and configure the PE agent on `<COMPILE.MASTER.EXAMPLE.COM>`.

2. From the command line of `<MASTER.EXAMPLE.COM>`, run `puppet cert --allow-dns-alt-names sign <COMPILE.MASTER.EXAMPLE.COM>`.

   **Note**: You cannot use the console to sign certs for nodes with DNS alt names.

3. From the command line on `<COMPILE.MASTER.EXAMPLE.COM>`, run `puppet agent -t`.

## Step 3: Classify the New Compile Master Node

[classification]: ./images/quick/classification.png

In this step, you will use the PE console to classify `<COMPILE.MASTER.EXAMPLE.COM>` so that it can function as a Puppet master and proxy requests to the PE certificate authority.

**To classify the new compile master node**:

1. From the console, click __Nodes__ > __Classification__.
2. Select the __PE Master__ group.
3. From the __Certname__ section, in the __Node name__ field, enter `<COMPILE.MASTER.EXAMPLE.COM>`.
4. Click __Pin node__.
5. Click the __Commit changes__ button.

## Step 4: Run Puppet on Selected Nodes

In this step, you need to run Puppet in the order specified so that certificate information for the new compile master can be added to PuppetDB's certificate whitelist. You will need to run Puppet on the PE console node for the RBAC whitelist as well.

>**Important**: The following Puppet runs **MUST** be done in the order listed in the following steps. The new compile master (`<COMPILE.MASTER.EXAMPLE.COM>`) must complete a full Puppet run before you run Puppet on the PuppetDB and console nodes.

**To run Puppet on selected nodes**:

1. SSH into `<COMPILE.MASTER.EXAMPLE.COM>` and run `puppet agent -t`.
2. SSH into `<PUPPETDB.EXAMPLE.COM>` and run `puppet agent -t`.
3. SSH into `<CONSOLE.EXAMPLE.COM>` and run `puppet agent -t`.
4. SSH into `<MASTER.EXAMPLE.COM>` and run `puppet agent -t`.

> **Done!** `<COMPILE.MASTER.EXAMPLE.COM>` is now a compile master node. To start using it, you will first need to add it to your load balancer.

## Using load balancers in a large environment installation

See [Using load balancers in an LEI](./install_lei_load.html) for more information. 

## A Note About PE File Sync

File Sync keeps your Puppet code in sync across multiple masters. When triggered by a web endpoint, file sync takes changes from your working directory on your Master of Masters (MoM) and deploys the code to a live code directory. File sync then automatically deploys that code onto all your compile masters, ensuring that all masters in a multi master configuration are kept in sync.

For more information about PE file sync, refer to [About File Sync](./cmgmt_filesync.html).

