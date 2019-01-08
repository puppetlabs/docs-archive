---
layout: default
title: "PE 2015.2» Troubleshooting » Installation"
subtitle: "Troubleshooting Installer Issues"
canonical: "/pe/latest/trouble_install.html"
---

Common Installer Problems
-----

Here are some common problems that can cause an install to go awry.

### Upgrades from 3.2.0 Can Cause Issues with Multi-Platform Agent Packages

Users upgrading from PE 3.2.0 to a later version of 3.x (including 3.2.3) will see errors when attempting to download agent packages for platforms other than the master. After adding `pe_repo` classes to the master for desired agent packages, errors will be seen on the subsequent Puppet run as PE attempts to access the requisite packages. The issue is caused by an incorrectly set parameter of the `pe_repo` class. It can be fixed as follows:

1. In the console, navigate to the node page for each master node where you wish to add agent packages.
2. On the master's node page, click __Edit__ and then, for the `pe_repo` parameter, click __Edit parameters__
3. Next to the `base_path` parameter, click __Reset value__
4. Save the parameter change and update the node.

Once this has been done, you should now be able to add new agent platforms without issue.

### Is DNS Wrong?

If name resolution at your site isn't quite behaving right, PE's installer can go haywire.

* Puppet agent has to be able to reach the Puppet master server at one of its valid DNS names. (Specifically, the name you identified as the master's hostname during the installer interview.)
* The Puppet master also has to be able to reach **itself** at the Puppet master hostname you chose during installation.
* If you've split the master and console components onto different servers, they have to be able to talk to each other as well.

### Are the Security Settings Wrong?

The installer fails in a similar way when the system's firewall or security group is restricting the ports Puppet uses.

* Puppet communicates on **ports 8140, 61613, and 443.** If you are installing the Puppet master and the console on the same server, it must accept inbound traffic on all three ports. If you've split the two components, the master must accept inbound traffic on 8140 and 61613 and the console must accept inbound traffic on 8140 and 443.
* If your Puppet master has multiple network interfaces, make sure it is allowing traffic via the IP address that its valid DNS names resolve to, not just via an internal interface.

### Did You Try to Install the Console Before the Puppet Master?

If you are installing the console and the Puppet master on separate servers and tried to install the console first, the installer may fail.

### Do You Need to Install Agents But Have No Internet Access?

Refer to [Installing Agents in a Puppet Enterprise Infrastructure without Internet Access](./install_agents.html#installing-agents-in-a-puppet-enterprise-infrastructure-without-internet-access)

### How Do I Recover From a Failed Install?

First, fix any configuration problem that may have caused the install to fail. See above for a list of the most common errors.

Next, run the uninstaller script. [See the uninstallation instructions in this guide](./install_uninstalling.html) for full details.

After you have run the uninstaller, you can safely run the installer again.

###  Problems with PE when upgrading your OS

Refer to [Performing Major OS Upgrades When Puppet Enterprise is Installed](./install_system_requirements.html#performing-major-os-upgrades-when-puppet-enterprise-is-installed).

* * *

- [Next: Troubleshooting Connections & Communications ](./trouble_comms.html)
