---
layout: default
title: "Installing the PE client tools package"
canonical: "/pe/latest/install_pe_client_tools.html"
---

The Puppet Enterprise client tools is a set of command line tools that let you access Puppet Enterprise services from a workstation that may or may not be managed by Puppet. Because you can safely run these tools remotely, you no longer need to SSH into the Puppet master to execute commands. Your permissions to see information and to take action are controlled by PE role-based access control (RBAC). Your activity is logged under your username rather than under `root` or the `pe-puppet` user.       

The package includes clients for the following services:

- [Puppet orchestrator](./orchestrator_intro.html): Tools that allow you to control the rollout of changes in your infrastructure, and provides the interface to the Puppet Application Orchestration service. These tools include `puppet-job` and `puppet-app`.
- [Puppet access](./rbac_token_auth.html#working-with-puppet-access): A tool used to authenticate yourself to the PE RBAC token-based authentication service so that you can use other capabilities and APIs.
- [Code Manager](./code_mgr_cli.html): A tool that provides the interface for the Code Manager and file sync services. This tool includes `puppet code`.
- [PuppetDB CLI]({{puppetdb}}/pdb_client_tools.html): A tool for working with PuppetDB, such as building queries and handling exports.

## Supported operating systems

The PE client tools package can be installed on the following platforms:

Operating system             | Version(s)                              | Arch
-----------------------------|-----------------------------------------|-----------
Red Hat Enterprise Linux     | 6, 7                                    | x86\_64
CentOS                       | 6, 7                                    | x86\_64
Oracle Linux                 | 6, 7                                    | x86\_64
Scientific Linux             | 6, 7                                    | x86\_64
SUSE Linux Enterprise Server | 11, 12                                  | x86\_64
Ubuntu                       | 12.04, 14.04. 16.04                     | x86\_64
Windows (Consumer OS)        | 7, 8, 10                                | x86\_64
Windows Server (Server OS)   | 2008r2, 2012 and 2012r2                 | x86\_64
Mac OS X	                 | 10.10, 10.11	                           | x86_64

### Matching client tools and PE versions

The PE client tools version aligns with a PE version, per the year and ordered number of the release ("x.y"). For example, if you're running PE 2017.1.1, you should use the 2017.1.1 client tools. 

In some cases, we may issue patch releases ("x.y.z") for PE or the client tools. You don't need to match patch numbers between PE and the client tools. Only the "x.y" numbers need to match. 

When you upgrade PE to a new "x.y" version, you should install the appropriate "x.y" version of PE client tools.

## Puppet master installation (default)

The `pe-client-tools` package is included in the PE installation tarball. When you install PE, it's automatically installed on the same node as the Puppet master. 

## PE-managed workstation installation

>**Note:** To install the `pe-client-tools` package on a Puppet agent node, it **must** be running the same OS as your Puppet master. 

1. Follow the Puppet agent installation documentation for [*nix](./install_agents.html), [Windows](tbd), [OS X](./install_osx.html) agents to install a Puppet agent that will act as your controller machine. 
2. In the PE console, [create a controller classification group](./console_classes_groups.html#creating-classification-node-groups), name it (e.g., PE Controller), and ensure its **Parent name** is set to **All Nodes**.
3. Select the **PE Controller** group, and add the `puppet_enterprise::profile::controller` class.

   PE uses the `puppet_enterprise::profile::controller` class to manage client tools global configuration files.

4. Add the controller machine to the **PE Controller** group.

   a. In the **Rules** tab, scroll down to the **pinned nodes** section.
   
   b. In the **Certname** field, enter the [certname]({{puppet}}/configuration.html#certname) of the node.
   
   c. Click **Pin node**, and then click the commit button.
   
5. [Run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes) on the controller machine.
6. Review [Global configuration file management](#global-configuration-file-management) to configure the client tools as needed. 
 

## Non-managed workstation installation

>**Note:** You can install the `pe-client-tools` package on any workstation running a [supported OS](#supported-operating-system). It does not need to be running the same OS as your Puppet master. 

Before you begin review the prerequisites for timekeeping, name resolution, and firewall configuration in the [system configuration requirements](./sys_req_sysconfig.html).

   >**Important**: Ensure that the following ports are available on the workstation:
   >
   >**Port 8143**: The orchestrator client uses this port to communicate with the Orchestration services running on the Puppet master.
   >
   >**Port 4433**: The Puppet access client uses this port to communicate with the RBAC service running on the Puppet master.
   >
   >**Port 8170**: If you use the Code Manager service, it requires this port.

### Install on a Linux workstation

#### Prepare the Linux workstation

1. **On the workstation**, create the following directory: `/etc/puppetlabs/puppet/ssl/certs`.

   For example, run `mkdir /etc/puppetlabs/puppet/ssl/certs`.
   
2. **On the Puppet master**, navigate to `/etc/puppetlabs/puppet/ssl/certs/` and copy `ca.pem` to the directory on the workstation you made in the previous step.
3. **On the workstation**, make sure the file permissions are correct by running `chmod 444 /etc/puppetlabs/puppet/ssl/certs/ca.pem`.
4. Verify that the checksum of `ca.pem` on the workstation matches the checksum of the same file on the Puppet master.
 
#### Install the with package management tools 

1. Download the [pe-client-tools package](http://puppetlabs.com/download-puppet-enterprise) for the appropriate supported OS platform to your workstation.
2. Unpack the tarball and navigate to the `packages/<PLATFORM>` directory.
3. Use your workstation's package management tools to install the pe-client-tools. (Package install tools vary from platform to platform.)

   For example, on RHEL platforms, run the following command:
   
   ~~~
   rpm -Uvh pe-client-tools-<VERSION-and-PLATFORM>.rpm
   ~~~
      
### Install on a Windows workstation

You can install the PE client tools on a Windows workstation using the setup wizard or the command line.

#### Prepare the Windows workstation

1. **On the workstation**, create the following directory: `C:\ProgramData\PuppetLabs\puppet\etc\ssl\certs`.

   For example, run `mkdir C:\ProgramData\PuppetLabs\puppet\etc\ssl\certs`.
   
2. **On the Puppet master**, navigate to `/etc/puppetlabs/puppet/ssl/certs/` and copy `ca.pem` to the directory on the workstation you made in the previous step.
3. **On the workstation**, make sure the file permissions for `C:\ProgramData\PuppetLabs\puppet\etc\ssl\certs\ca.pem` are set to read-only.
4. Verify that the checksum of `ca.pem` on the workstation matches the checksum of the same file on the Puppet master.

#### Install with the setup wizard

1. Download the [Windows pe-client-tools package](http://puppetlabs.com/download-puppet-enterprise).
2. Double click the pe-client-tools .msi file.
3. Follow the prompts to the accept license agreement and choose the installation location. 
4. Click **Install**. 

#### Install from the command line

1. Download the [Windows pe-client-tools package](http://puppetlabs.com/download-puppet-enterprise).
2. From the command line, run `msiexec /i <PATH TO PE-CLIENT-TOOLS.MSI> TARGETDIR="<INSTALLATION DIRECTORY>"`.

   Note that `TARGETDIR` is optional.

To start using the client tools on your Windows workstation, open the **PE ClientTools Console** from the **Start** menu.  

### Install on an OS X workstation

You can install the PE client tools on an OS X workstation using Finder or the command line.

#### Prepare the OS X workstation

1. **On the workstation**, create the following directory: `/etc/puppetlabs/puppet/ssl/certs`.

   For example, run `mkdir -p /etc/puppetlabs/puppet/ssl/certs`.
   
2. **On the Puppet master**, navigate to `/etc/puppetlabs/puppet/ssl/certs/` and copy `ca.pem` to the directory on the workstation you made in the previous step.
3. **On the workstation**, make sure the file permissions are correct by running `chmod 444 /etc/puppetlabs/puppet/ssl/certs/ca.pem`.
4. Verify that the checksum of `ca.pem` on the workstation matches the checksum of the same file on the Puppet master.

#### Install with OS X Finder

1. Download the [OS X pe-client-tools package](http://puppetlabs.com/download-puppet-enterprise).
2. Open the pe-client-tools .dmg and click the installer .pkg.
3. Follow the instructions in the installer dialog.

#### Install from the command line

1. Download the [OS X pe-client-tools package](http://puppetlabs.com/download-puppet-enterprise).
2. Run `sudo hdiutil mount <DMGFILE>`. (Replace `DMGFILE` with the package you downloaded.)

   You will see a line that ends, `/Volumes/pe-client-tools-<VERSION>`. This is the mount point for the virtual volume created from the disk image.

3. Run `cd /Volumes/pe-client-tools-VERSION`.
4. Run `sudo installer -pkg pe-client-tools-<VERSION>-installer.pkg -target /`.
5. Run `cd ~`, and then run `sudo umount /Volumes/pe-client-tools-VERSION`.
 

## Configuring and using client tools

After installing the tools, see the corresponding documentation to configure and start using each client tool. Note that for the Puppet Access, Puppet code, or Puppet orchestrator clients, you can use a global configuration file for each service, or create a configuration file for each service on a per user basis. Instructions for creating configuration files are in the corresponding documentation.

### Global configuration file management

If you're running the Puppet code or Puppet orchestrator clients from a PE-managed machine, you can have PE manage their global configuration file with the `puppet_enterprise::profile::controller` class. This class manages global configuration files in `/etc/puppetlabs/client-tools/`.
 
Note that if you're running a client from a workstation, you will need to create the global file and populate it with the correct configuration file settings. PE cannot manage a configuration file from a non-PE-managed workstation.

Details about configuration files are in the corresponding documentation.

- [Puppet orchestrator client](./orchestrator_install.html#puppet-orchestrator-configuration-files)
- [Puppet access client](./rbac_token_auth.html#working-with-puppet-access)
- [Code Manager `puppet code` client](./code_mgr_cli.html)
- [PuppetDB CLI client]({{puppetdb}}/pdb_client_tools.html)


