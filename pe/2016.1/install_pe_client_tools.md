---
layout: default
title: "Installing the PE client tools package"
canonical: "/pe/latest/install_pe_client_tools.html"
---

The Puppet Enterprise client tools is a set of command line tools that let you access Puppet Enterprise services from a workstation that may or may not be managed by Puppet. Because you can safely run these tools remotely, you no longer need to SSH into the Puppet master to execute commands. Your permissions to see information and to take action are controlled by PE role-based access control (RBAC). Your activity is logged under your username rather than under `root` or the `pe-puppet` user.       

The package includes clients for the following services:

- **Puppet orchestrator**: Tools that allow you to control the rollout of changes in your infrastructure, and provides the interface to the Puppet Application Orchestration service. These tools include `puppet-job` and `puppet-app`.
- **Puppet Access**: A client used to authenticate yourself to the PE RBAC token-based authentication service so that you can use other capabilities and APIs.
- **Razor**: The client for Razor, the provisioning application for deploying bare metal systems.
- **Code Manager**: A tool that provides the interface for the Code Manager and file sync services. This tool includes `puppet-code`.

## Supported platforms

The PE client tools package can be installed on the following platforms:

Operating system             | Version(s)                              | Arch
-----------------------------|-----------------------------------------|-----------
Red Hat Enterprise Linux     | 6, 7                                    | x86\_64
CentOS                       | 6, 7                                    | x86\_64
Oracle Linux                 | 6, 7                                    | x86\_64
Scientific Linux             | 6, 7                                    | x86\_64
SUSE Linux Enterprise Server | 11, 12                                  | x86\_64
Ubuntu                       | 12.04, 14.04                            | x86\_64

## Installing PE client tools

### Puppet master installation (default)

The `pe-client-tools` package is included in the PE installation tarball. When you install PE, it's automatically installed on the same node as the Puppet master. 

### PE-managed workstation installation

>**Note:** To install the `pe-client-tools` package on a Puppet agent node, it **must** be running the same platform as the Puppet master. 

1. Follow the [Puppet agent installation documentation](./install_agents.html) to install a Puppet agent that will act as your controller machine. 
2. In the PE console, [create a controller classification group](./console_classes_groups.html#creating-classification-node-groups), name it (e.g., PE Controller), and ensure it's **Parent name** is set to **All Nodes**.
3. Select the **PE Controller** group, and add the `puppet_enterprise::profile::controller` class.

   PE uses the `puppet_enterprise::profile::controller` class to manage client tools global configuration files.

4. Add the controller machine to the **PE Controller** group.

   a. In the **Rules** tab, scroll down to the **pinned nodes** section.
   
   b. In the **Certname** field, enter the [certname]({{puppet}}/configuration.html#certname) of the node.
   
   c. Click **Pin node**, and then click the commit button.
   
5. Run Puppet on the controller machine.
6. Review [Global configuration file management](#global-configuration-file-management) to configure the client tools as needed. 
 

### Non-managed workstation installation

You can install the `pe-client-tools` package on any workstation running a [supported OS platform](#supported-platforms). 

The pe-client-tools package requires the puppet-agent package. However, the workstation you're installing on needn't be managed by Puppet.

**To install the pe-client-tools package on a workstation**:

1. Review the prerequisites for timekeeping, name resolution, and firewall configuration in the [system configuration requirements](./install_system_requirements.html#system-configuration).

   >**Important**: Ensure that the following ports are available on the workstation:
   >
   >**Port 8143**: The orchestrator client uses this port to communicate with the Orchestration services running on the Puppet master.
   >
   >**Port 4433**: Puppet Access uses this port to communicate with the Puppet Access/RBAC service running on the Puppet master.
   >
   >**Port 8151**: The Razor client uses this port to communicate with the Razor server.
   >
   >**Port 8170**: If you use the Code Manager service, it requires this port.

2. Copy the Puppet Enterprise tarball for the appropriate supported OS platform to your workstation.
3. Unpack the tarball and navigate to the `packages/<PLATFORM>` directory.
4. Use your workstation's package management tools to install the puppet-agent and pe-client-tools. (Package install tools vary from platform to platform.)

   For example, on RHEL platforms, run the following commands:
   
   a. `rpm -Uvh puppet-agent-<VERSION-and-PLATFORM>.rpm`
   
   b. `rpm -Uvh pe-client-tools-<VERSION-and-PLATFORM>.rpm`
      
5. **On the workstation**, create the following directory: `/etc/puppetlabs/puppet/ssl/certs`.
6. **On the Puppet master**, navigate to `/etc/puppetlabs/puppet/ssl/certs/` and copy `ca.pem` to the directory on the workstation you made in the previous step.
7. **On the workstation**, make sure the file permissions are correct by running `chmod 444 /etc/puppetlabs/puppet/ssl/certs/ca.pem`.
8. Verify that the checksum of `ca.pem` on the workstation matches the checksum of the same file on the Puppet master.
 

## Configuring and using client tools

After installing the tools, see the following documentation to configure and start using each client tool. Note that for the Puppet Access, Puppet code, or Puppet orchestrator clients, you can use a global configuration file for each service, or create a configuration file for each service on a per user basis. Instructions for creating configuration files are in the corresponding documentation.

### Global configuration file management

If you're running the Puppet code or Puppet orchestrator clients from a PE-managed machine, you can have PE manage their global configuration file with the `puppet_enterprise::profile::controller` class. This class manages global configuration files in `/etc/puppetlabs/client-tools/`.
 
Note that if you're running a client from a workstation, you will need to create the global file and populate it with the correct configuration file settings. PE cannot manage a configuration file from a non-PE-managed workstation.

Details about configuration files are in the corresponding documentation.

- [Puppet orchestrator client](./orchestrator_install.html#configure-the-puppet-orchestrator-client)
- [Razor client](./razor_install.html#install-the-razor-client)
- [Code Manager `puppet-code` client](./code_mgr_cli.html)



