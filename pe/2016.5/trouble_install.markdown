---
layout: default
title: "Troubleshooting the installer"
canonical: "/pe/latest/trouble_install.html"
---

Common installer problems
-----

Here are some common problems that can cause an install to go awry.

### Is DNS wrong?

If name resolution at your site isn't quite behaving right, PE's installer can go haywire.

* Puppet agent has to be able to reach the Puppet master server at one of its valid DNS names. (Specifically, the name you identified as the master's hostname during the installer interview.)
* The Puppet master also has to be able to reach **itself** at the Puppet master hostname you chose during installation.
* If you've split the master and console components onto different servers, they have to be able to talk to each other as well.

### Are the security settings wrong?

The installer fails in a similar way when the system's firewall or security group is restricting the ports Puppet uses.

* Puppet communicates on **ports 8140, 61613, and 443.** If you are installing the Puppet master and the console on the same server, it must accept inbound traffic on all three ports. If you've split the two components, the master must accept inbound traffic on 8140 and 61613 and the console must accept inbound traffic on 8140 and 443.
* If your Puppet master has multiple network interfaces, make sure it is allowing traffic via the IP address that its valid DNS names resolve to, not just via an internal interface.

### Did you try to install the console before the Puppet master?

If you are installing the console and the Puppet master on separate servers and tried to install the console first, the installer may fail.

### Do you need to install agents but have no internet access?

Refer to [Installing agents in a Puppet Enterprise infrastructure without internet access](./install_agents.html#installing-agents-in-a-puppet-enterprise-infrastructure-without-internet-access)

### How do I recover from a failed install?

The PE installer is idempotent, meaning that you can run it as many times as needed without affecting the outcome. If you encounter errors during installation, you can fix them and run the installer again.

###  Problems with PE when upgrading your OS

Refer to [Performing major OS upgrades when Puppet Enterprise is installed](./sys_req_os.html#performing-major-os-upgrades-when-puppet-enterprise-is-installed).


