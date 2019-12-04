---
layout: default
title: "Installing the PE license key"
canonical: "/pe/latest/install_license_key.html"
---

You can download and install Puppet Enterprise on up to 10 nodes at no charge, and no license key is needed. When you have 11 or more active nodes and no license key, PE logs license warnings until you install an appropriate license key.

1. When you have 11 or more active nodes, purchase a license key file

    + From the [Puppet Enterprise pricing page](https://puppet.com/product/pricing) or
    + By contacting our sales team. Find contact information on the [Ready to buy Puppet Enterprise? page](https://buy.puppet.com/).

    We'll send you a `license.key` file. If you don't receive your license key file or have misplaced it, contact <sales@puppet.com>.

2. On the Puppet master, install the license key by copying the file to `/etc/puppetlabs/license.key`.

3. Check the number of active nodes in your deployment, and the number of licenses and expiration date for your license.

    + On the Puppet master, run `puppet license`.
    + In the PE console, click on **License.**

        **Note:** The PE console caches license information. Changes to your license key file (for example adding or renewing licenses) might not show in the console for up to 24 hours.

4. If you need more licenses:

    + Free up licenses by [removing inactive nodes nodes from your deployment](https://docs.puppet.com/pe/latest/node_deactivation.html). By default, unused nodes are deactivated automatically after seven days with no activity (no new facts, catalog or reports).

    + If you’re an existing Puppet Enterprise customer and would like to add nodes, contact your account manager to buy additional licenses. Otherwise, contact our sales team. Find contact information on the [Ready to buy Puppet Enterprise? page](https://buy.puppet.com/).

5. If your license is expiring soon, contact our sales team. Find contact information on the [Ready to buy Puppet Enterprise? page](https://buy.puppet.com/).