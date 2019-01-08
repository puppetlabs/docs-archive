---
layout: default
title: "Installing Cisco IOS-XR Agents"
canonical: "/pe/latest/install_iosxr.html"
---

Installing Cisco IOS-XR Agents

Puppet Enterprise supports Cisco IOS-XR release 6.0 and higher as a platform for running Puppet agents on their network switches. Supported Cisco switches include:

* Cisco IOS-XR NCS5500 (IOS-XR 6.0)
* Cisco IOS-XR XR9kv (IOS-XR 6.0)

>**Important:** Currently the Puppet agent only supports the built-in package, file, and service resources.

These instructions assume you’ve already [installed Puppet Enterprise](./install_basic.html) and have a compatible Cisco IOS-XR switch running the minimum required version of IOS-XR. Installing the Cisco agent requires that you install the [puppetlabs-ciscopuppet module](https://forge.puppetlabs.com/puppetlabs/ciscopuppet), which contains the types and providers needed to configure your Cisco IOS-XR switches.

##  Step 1: Install the puppetlabs-ciscopuppet Module

On your Puppet master, run, `puppet module install puppetlabs-ciscopuppet`.

##  Step 2: Install the Puppet Agent

These instructions assume you have networking and DNS configured for the bash environment. Detailed instructions can be found in the section “Puppet Agent Environment: bash-shell” from [README-AGENT-INSTALL.md](https://forge.puppetlabs.com/puppetlabs/ciscopuppet) in the ciscopuppet module.

>**Note:** To install the Puppet agent, we recommend 400 MB of free disk space on bootflash.

**To install the Puppet agent with Bash:**

1. Start a root Bash shell with networking enabled. From the command line of your network switch, run the following commands:

   ~~~
   run bash
   sudo ip netns exec management bash
   ~~~
    
2. Install the Puppet Collections PC1 repository package for cisco_wrlinux:

   ~~~
   bash-4.2# yum install http://yum.puppetlabs.com/puppetlabs-release-pc1-cisco-wrlinux-7.noarch.rpm
   ~~~
   
   >**Note:** Make sure you install the repository which has "cisco-wrlinux-7" in the filename, which works on IOS-XR devices. Any files which have "cisco-wrlinux-5" in the filename will not run on IOS-XR devices and are intended for Cisco NX-OS devices instead.

3. Install the Puppet agent:

   ~~~
   yum install puppet-agent
   ~~~

4. The Puppet agent is now installed under `/opt/puppetlabs`. Add the `bin` and `lib` paths to your login shell’s `PATH`:

   ~~~
   export PATH=$PATH:/opt/puppetlabs/puppet/bin:/opt/puppetlabs/puppet/lib
   ~~~

##  Uninstalling Puppet Agent with Bash or “Guest Shell”

The commands to uninstall the Puppet agent are the same for both Bash and "guest shell" environments. 

1. Remove the Puppet agent:

   ~~~
   sudo su -
   yum remove puppet-agent
   ~~~

2. Remove the Puppet Collections PC1 repository package package:

   ~~~
   yum remove puppetlabs-release-pc1
   ~~~
   
##  Additional Resources

Refer to the [puppet-ciscopuppet module](https://forge.puppetlabs.com/puppetlabs/ciscopuppet/readme) for more information about using the module on your IOS-XR network switch.