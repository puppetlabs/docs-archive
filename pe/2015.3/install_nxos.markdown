---
layout: default
title: "Installing Cisco NX-OS Agents"
canonical: "/pe/latest/install_nxos.html"
---


Puppet Enterprise supports Cisco NX-OS release 7.0 and higher as a platform for running Puppet agents on their network switches. Supported Cisco switches include:

* Cisco Nexus 95xx series switches
* Cisco Nexus 93xx series switches
* Cisco Nexus 31xx series switches
* Cisco Nexus 30xx series switches

These instructions assume you've already [installed Puppet Enterprise](./install_basic.html) and have a compatible Cisco Nexus switch running the minimum required version of NX-OS. Installing the Cisco agent requires that you install the [puppet-ciscopuppet module](https://forge.puppetlabs.com/puppetlabs/ciscopuppet), which contains the types and providers needed to configure your Cisco Nexus switches.

## Step 1: Install the puppetlabs-ciscopuppet Module

On your Puppet master, run, `puppet module install puppetlabs-ciscopuppet`.

## Step 2: Perform a Bash Install or a Guest Shell Install

NX-OS supports two possible environments for running third-party software: bash shell or guest shell. Choose one environment for running Puppet Agents and follow the appropriate steps below. (Note that you can run Puppet from either environment but not both at the same time.)

* **Bash**: This is the native Linux environment underlying NX-OS. It is disabled by default.
* **Guest**: This is a secure Linux container environment running CentOS. It is enabled by default on most Nexus platforms.

>**Note**: To install the Puppet agent, we recommend 400 MB of free disk space on bootflash.

### Installing the Puppet Agent with Bash 

Follow the steps in this section only if you plan to run the Puppet agent from the bash environment.

>**Tip**: The bash environment is disabled by default. If you haven't enabled it already, you should consider using the “guest shell” environment instead. 

These instructions assume you have networking and DNS configured for the bash environment. Detailed instructions on doing this can be found in the section ["Puppet Agent Environment: bash-shell" from README-AGENT-INSTALL.md in the ciscopuppet module](https://forge.puppetlabs.com/puppetlabs/ciscopuppet).

**To install the Puppet agent with Bash**: 

1. Start a root Bash shell with networking enabled. From the command line of your network switch, run the following commands: 

   ~~~
   run bash
   sudo ip netns exec management bash
   ~~~ 

2. Install the Puppet Collections PC1 repository package for cisco_wrlinux:

   ~~~
   bash-4.2# yum install http://yum.puppetlabs.com/puppetlabs-release-pc1-cisco-wrlinux-5.noarch.rpm
   ~~~
   
3. Install the Puppet agent:

   ~~~
   yum install puppet-agent
   ~~~

4. The Puppet agent is now installed under `/opt/puppetlabs`. Add the `bin` and `lib` paths to your login shell's `PATH`:

   ~~~
   export PATH=$PATH:/opt/puppetlabs/puppet/bin:/opt/puppetlabs/puppet/lib
   ~~~ 
   
### Installing the Puppet Agent with “Guest Shell”

Follow the steps in this section only if you plan to run the Puppet agent from the “guest shell” environment.

1. Start a root guest shell, and install the Puppet Collections PC1 repository package for EL-7:

   ~~~
   sudo su -
   yum install http://yum.puppetlabs.com/puppetlabs-release-pc1-el-7.noarch.rpm
   ~~~

2. Install the Puppet agent:

   ~~~
   yum install puppet-agent
   ~~~

3. The Puppet agent is now installed under `/opt/puppetlabs`. Add the `bin` and `lib` paths to your login shell's `PATH`:

   ~~~
   export PATH=$PATH:/opt/puppetlabs/puppet/bin:/opt/puppetlabs/puppet/lib
   ~~~ 

## Uninstalling Puppet Agent with Bash or “Guest Shell”

The commands to uninstall the Puppet agent are the same for both Bash and “guest shell” environments.

1. Remove the Puppet agent:

   ~~~
   sudo su -
   yum remove puppet-agent
   ~~~

2. Remove the Puppet Collections PC1 repository package package:

   ~~~
   yum remove puppetlabs-release-pc1
   ~~~

## Additional Resources

Refer to the [puppet-ciscopuppet module](https://forge.puppetlabs.com/puppetlabs/ciscopuppet) for more information about using the module on your network switch.
