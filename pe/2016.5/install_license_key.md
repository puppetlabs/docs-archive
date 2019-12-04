---
layout: default
title: "Installing the PE license key"
canonical: "/pe/latest/install_license_key.html"
---

When you purchase Puppet Enterprise, you are sent a `license.key` file that lists how many nodes you can deploy. PE logs license warnings until you copy this file to the Puppet master.

## Install the PE license key

In the PE console, the **Licenses** menu shows you the number of nodes that are currently active and the number of nodes still available on your current license. If the number of available licenses is exceeded, PE displays a warning. The number of licenses used is determined by the number of active nodes known to PuppetDB. 

Unused nodes will be deactivated automatically after seven days with no activity (no new facts, catalog or reports), or you can use `puppet node deactivate` for immediate results.

You can download and install Puppet Enterprise on up to 10 nodes at no charge, and no license key is needed. If you have lost your license key file, or need to purchase one, see the [Puppet Enterprise pricing page](https://puppet.com/product/pricing), or contact Puppet at <sales@puppet.com> or (877) 575-9775.

To install the license key:

1. On the Puppet master, copy the file to `/etc/puppetlabs/license.key`. 
