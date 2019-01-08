---
layout: default
title: "PE 2015.2 » Installing » LEI Compile Masters"
subtitle: "Compile Master Installation"
canonical: "/pe/latest/install_multimaster.html"
---

>**Note**: If you need to upgrade compile masters running on the Ubuntu platform, see [Puppet Agent Service Does Not Properly Stop When Upgrading Compile Masters on Ubuntu Platforms](./release_notes_known_issues.html#puppet-agent-service-does-not-properly-stop-when-upgrading-compile-masters-on-ubuntu-platforms) for a full description and workaround of this known issue.

The following guide provides instructions for adding additional Puppet masters to large environment installations (those managing more than 1000 agent nodes). Using additional Puppet masters in an LEI will provide quicker, more efficient compilation times as multiple masters can share request load when agent nodes run. Typically, in LEI configurations, the additional Puppet masters are referred to as **compile masters** and your original Puppet master is referred to as the CA server or **Master of Masters (MoM)**.

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

### Step 1: Install Puppet Enterprise

1. Follow the [PE split installation documentation](./install_pe_split.html) to install PE.
2. Log in to the console.
3. Continue to [Step 2: Install Additional Puppet Master Node](./install_multimaster.html#step-2-install-compile-master-node).

### Step 2: Install Compile Master Node

In this step, you will install the additional Puppet agent on `<COMPILE.MASTER.EXAMPLE.COM>`. You must perform this step to install the Puppet agent on the new compile master node.

**Warning**: This machine (e.g., the new compile master) should NOT already have a Puppet agent installed.

**To install the additional compile master agent**:

1. SSH into `<COMPILE.MASTER.EXAMPLE.COM>` and run `curl -k https://<MASTER.EXAMPLE.COM>:8140/packages/current/install.bash | sudo bash -s main:dns_alt_names=<COMMA-SEPARATED LIST OF ALT NAMES FOR THE PUPPET MASTER>`.

   **Note**: The `dns_alt_names` value should be set to a comma-separated list of any alternative names that may be used by Puppet agents to connect to the master. The installation uses "puppet" by default.

   This will install and configure the PE agent on `<COMPILE.MASTER.EXAMPLE.COM>`.

2. From the command line of `<MASTER.EXAMPLE.COM>`, run `puppet cert --allow-dns-alt-names sign <COMPILE.MASTER.EXAMPLE.COM>`.

   **Note**: You cannot use the console to sign certs for nodes with DNS alt names.

3. From the command line on `<COMPILE.MASTER.EXAMPLE.COM>`, run `puppet agent -t`.

4. Continue to [Step 3: Classify the New Compile Master Node](#step-3-classify-the-new-compile-master-node).

### Step 3: Classify the New Compile Master Node

[classification]: ./images/quick/classification.png

In this step, you will use the PE console to classify `<COMPILE.MASTER.EXAMPLE.COM>` so that it can function as a Puppet master and proxy requests to the PE certificate authority.

> **Important**: For fresh installations of PE 2015.2, node groups in the classifier are created and configured during the installation process. For upgrades, these groups and classes are created but no nodes are pinned to them. This is done to help prevent errors during the upgrade process. If you’re performing these steps after an upgrade to 2015.2, refer to [the PE Master group preconfiguration docs](./console_classes_groups_preconfigured_groups.html#the-pe-master-group) to ensure the PE Master group has the correct classes .

**To classify the new compile master node**:

1. From the console, click __Nodes__ > __Classification__.
2. Select the __PE Master__ group.
3. From the __Rules__ tab, in the __Node name__ field, enter `<COMPILE.MASTER.EXAMPLE.COM>`.
4. Click __Pin node__.
5. Click the __Commit changes__ button.
6. Continue to [Step 4: Run Puppet on Selected Nodes](#step-4-run-puppet-on-selected-nodes)

### Step 4: Run Puppet on Selected Nodes

In this step, you need to run Puppet in the order specified so that certificate information for the new compile master can be added to PuppetDB's certificate whitelist. You will need to run Puppet on the PE console node for the RBAC whitelist as well.

>**Important**: The following Puppet runs **MUST** be done in the order listed in the following steps. The new compile master (`<COMPILE.MASTER.EXAMPLE.COM>`) must complete a full Puppet run before you run Puppet on the PuppetDB and console nodes.

**To run Puppet on selected nodes**:

1. SSH into `<COMPILE.MASTER.EXAMPLE.COM>` and run `puppet agent -t`.
2. SSH into `<PUPPETDB.EXAMPLE.COM>.com` and run `puppet agent -t`.
3. SSH into `<CONSOLE.EXAMPLE.COM>` and run `puppet agent -t`.

> **Done!** `<COMPILE.MASTER.EXAMPLE.COM>` is now a compile master node. To start using it, you will first need to add it to your load balancer.

### A Note About Load Balancers

Load balancing is a critical need for PE deployments using multiple Puppet masters. Unfortunately, load balancer integration is beyond the scope of this document. However, when configuring load balancers with your PE masters, you need to ensure traffic can pass through port **8140**---this is the port that handles Puppet master and CA traffic.

#### Using Load Balancers in a Large Environment Installation

If you use a load balancer in your LEI, you may want to point the agent installation script at the load balancer when installing new agents. However, if you bring in any new compile masters, they will need to be pointed at the CA server or MoM. In order to successfully use a load balancer, you'll need to:

1. Enable additional compile master installation (such that those agents don't request configuration via the load balancer) 
2. Enable agent installation via your load balancer

Step 1: Enable additional compile master installation

1. From the console, click __Nodes__ > __Classification__.
2. From the __Classification__ page, in the __Node group name__ field, enter a name such as "PE CA pe\_repo override." (Note, do not add the quotes.)
3. From the __Parent name__ drop-down list, select the __PE Master__ group, and then click __Add group__.
4. In the __PE CA pe_repo override__ group, from the __Rules__ tab, in the __Node name__ field, enter the certname of your CA server.
5. Click the __Classes__ tab, and in the __Add new class__ field, enter "pe\_repo," and select __Add class__. (Note, do not add the quotes.)
6. From the __Parameter__ drop-down list, select __master__.
7. In the __Value__ field, enter the FQDN of your original Puppet master/CA server (e.g. in the examples above, `<MASTER.EXAMPLE.COM>`).
8. Click __Add parameter__ and then the __Commit change__ button.

This ensures that when you install a compile master it will correctly download packages from the CA server/original Puppet Master. All compile masters need to have the CA set as their server in `puppet.conf`. 

Step 2: Enable agent installation via your load balancer  

1. In the __PE Master__ group, click the __Classes__ tab, and find the __pe_repo__ class.
2. From the __Parameter__ drop-down list, select __master__.
3. In the __Value__ field, enter the address your load balancer resolves to (e.g., `LOADBALANCER.EXAMPLE.COM`).
4. Click __Add parameter__ and then the __Commit change__ button.

This ensures that when you install a Puppet agent, it will correctly download packages from any compile master it might interact with during the install process.

### A Note About File Syncing

In a multi-master environment, you want to make sure your manifests, modules, and any Hiera data are properly synced between Puppet masters.  Depending on your security and infrastructure needs, there are a range of options for setting up file syncing between your Puppet masters. We recommend choosing from one of the following methods:

- [r10K](./r10k.html)
- Network File Systems (NFS)
- Remote Sync (rsync)



