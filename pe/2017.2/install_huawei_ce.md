---
layout: default
title: "Installing Huawei CloudEngine Agents"
canonical: "/pe/latest/install_huawei_ce.html"
---

Puppet and Puppet Enterprise support HuaweiOS 6.0 and higher for running Puppet agents on some models of Huawei network switches. Supported switches include:

* Huawei CloudEngine 12800 series switches (CE12804/CE12808/CE12812/CE12816)
* Huawei CloudEngine 8800 and 7900 series switches (CE8860/CE7850)
* Huawei CloudEngine 6800 series switches (CE6850/CE6850HI)

These instructions assume you've already installed [Puppet]({{puppet}}/install_pre.html) or [Puppet Enterprise](./install_basic.html) and have a compatbile Huawei CloudEngine switch running HuaweiOS 6.0 (firmware version V100R006C00) or later. The Puppet agent runs in the LXC (container) environment, which is based on Debian Linux 8 for the PowerPC architecture. 

## Prerequisites

The LXC (container) OS that the Puppet agent gets installed into has a separate network environment that needs to be configured before you can access it. The specifics of how to configure this environment depend on your datacenter network policy and are outside the scope of this document.  

**To perform the following installation steps you must be able to ssh as the root user to the LXC's IP address.**

You also need the IP address and fully qualified domain name (FQDN) of your Puppet master.

## Install the HuaweiOS Puppet Agent

>**Note:** Run the following commands as the `root` user.

1. Log into the LXC environment and download the apt repository package for HuaweiOS. Run `wget http://apt.puppetlabs.com/puppetlabs-release-pc1-huaweios.deb`. 
2. Install the repository package with `dpkg -i puppetlabs-release-pc1-huaweios.deb`.
3. Run `apt-get update`.
4. Install the Puppet agent with `apt-get install puppet-agent`. 
5. Edit `/etc/hosts` so that it includes the IP address of your Puppet master, as well as its FQDN and default DNS alternate name, "puppet".
6. On the switch, run the Puppet agent with `/opt/puppetlabs/bin/puppet agent -t`. 

   This creates a certificate signing request (CSR) for the Puppet agent that you will need to sign from the Puppet master.
   
7. On the Puppet master, sign the Puppet agent’s CSR. Run `puppet cert sign <NETWORK DEVICE'S FQDN>`.
8. On the network device, run the Puppet agent again to complete the installation. Run `/opt/puppetlabs/bin/puppet agent -t`.

   On this run, the Puppet agent will retrieve its catalog and be fully functional.

## Uninstall the HuaweiOS Puppet Agent

1. Uninstall the puppet-agent and agent packages with `apt-get remove puppet-agent puppetlabs-release-pc1`. 

   This will remove both the agent itself and the repository package that points to Puppet's APT server. 

2. To clear Puppet's APT repsoitory info from your apt cache, run `apt-get update`.
3. On the Puppet master, revoke the cert for the Puppet agent with `puppet cert clean <NETWORK DEVICE'S FQDN>`.

   This revokes the agent certificate and deletes related files on the Puppet master. This prevents SSL collisions should you want to reinstall the Puppet agent on the same device.