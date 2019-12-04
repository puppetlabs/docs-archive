---
layout: default
title: "Installing Cumulus Linux Puppet agents"
canonical: "/pe/latest/install_cumulus.html"
---

Puppet Enterprise supports the Cumulus Linux operating system as an agent-only platform. These instructions assume you've already installed a device that supports [Cumulus Linux](http://cumulusnetworks.com/support/linux-hardware-compatibility-list/), as well as having already [installed Puppet Enterprise](./install_basic.html).

In order to install the Cumulus Linux Puppet agent, you must have access to the Cumulus account on the network device.

Before beginning, you also need to gather:

- The IP address and the fully qualified domain name (FQDN) of your Puppet master server.
- The FQDN of the network device that you're installing the Cumulus Linux on.

## Install the Cumulus Linux Puppet agent

1. From the network switch, run `wget http://apt.puppetlabs.com/puppetlabs-release-pc1-cumulus.deb`.
2. Install the agent package with `sudo dpkg -i puppetlabs-release-pc1-cumulus.deb`. 
3. Run `sudo apt-get update`. 
4. Install the Puppet agent with `sudo apt-get install puppet-agent`.
5. Edit `/etc/hosts` so that it includes the IP address of your Puppet master, as well as its FQDN and its default DNS alt name, `puppet`.
6. Run the Puppet agent on the switch with `sudo /opt/puppetlabs/bin/puppet agent -t`.

   This creates a certificate signing request (CSR) for the Puppet agent that you will need to sign from the Puppet master.

7. On the Puppet master, sign the Puppet agent's CSR. Run `puppet cert sign <NETWORK DEVICE'S FQDN>`.
8. On the network device, complete the installation by running Puppet with `sudo /opt/puppetlabs/bin/puppet agent -t`.

   The Puppet agent will retrieve its catalog and will now be fully functional.

> **Next steps**: Now it's time to begin managing ports, licenses, and interfaces on your network device. Refer to the [Cumulus Linux page](https://forge.puppetlabs.com/cumuluslinux/) on the Puppet Forge for information about modules to get you started.

## Uninstall the Cumulus Linux Puppet agent

1. Uninstall the puppet-agent and and agent packages with `sudo apt-get remove puppet-agent puppetlabs-release-pc1`.
2. On the Puppet master, revoke the cert for the Puppet agent with `puppet cert clean <NETWORK DEVICE'S FQDN>`.

   This revokes the agent certificate and deletes related files on the Puppet master. This prevents SSL collisions should you want to reinstall the Puppet agent on the same device.


































[downloadpe]: https://puppetlabs.com/download-puppet-enterprise
