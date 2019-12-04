---
layout: default
title: "Purchasing and installing the PE license key"
canonical: "/pe/latest/install_license_key.html"
---

{:.concept}
You can download and install Puppet Enterprise on up to 10 nodes at no charge, and no license key is needed. When you have 11 or more active nodes and no license key, PE logs license warnings until you install an appropriate license key.

{:.section}
## Getting licensed

When you have 11 or more active nodes, purchase a license key file.

+ New PE customers -- Purchase the license key file from the [Puppet Enterprise pricing page](https://puppet.com/product/pricing) or by contacting our sales team. Find contact information on the [Ready to buy Puppet Enterprise? page](https://buy.puppet.com/).

+ Existing PE customers -- Add nodes by contacting your sales representative or emailing <sales@puppet.com>.

We'll send you a `license.key` file. If you don't receive your license key file or have misplaced it, contact <sales@puppet.com>.

If you need more licenses, free up licenses by [removing inactive nodes nodes from your deployment](https://docs.puppet.com/pe/latest/node_deactivation.html). By default, unused nodes are deactivated automatically after seven days with no activity (no new facts, catalog or reports).

{:.task}
## Install a license key

Install the `license.key` file you obtained from Puppet to upgrade from a test installation to an active installation.

1. Install the license key by copying the file to `/etc/puppetlabs/license.key`.

   * For monolithic installations, add the file to the Puppet master node.
   * For split installations, add the file to the Puppet master and console nodes. 

   **Note:** The PE console caches license information. Changes to your license key file (for example adding or renewing licenses) might not show in the console for up to 24 hours.

If you'd like to upgrade or renew your annual license, please contact your sales representative or email <sales@puppet.com>.

{:.task}
## Verify installed licenses and active nodes

Check the number of active nodes in your deployment and the number of licenses and expiration date for your license.

1. On the Puppet master, run `puppet license`.
2. In the PE console, click on **License.**

    **Note:** The PE console caches license information. Changes to your license key file (for example adding or renewing licenses) might not show in the console for up to 24 hours.