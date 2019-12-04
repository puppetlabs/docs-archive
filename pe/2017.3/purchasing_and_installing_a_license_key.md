# Purchasing and installing a license key

You can download and install Puppet Enterprise on up to 10 nodes at no charge, and no license key is needed. When you have 11 or more active nodes and no license key, PE logs license warnings until you install an appropriate license key.

## Getting licensed

When you have 11 or more active nodes, purchase a license key file.

-   New PE customers — Purchase the license key file from the Puppet website or by contacting our sales team. Find contact information on the [Ready to buy Puppet Enterprise?](https://buy.puppet.com/) page.

-   Existing PE customers — Add nodes by contacting your sales representative or emailing sales@puppet.com.


If you need more licenses, free up licenses by removing inactive nodes nodes from your deployment. By default, unused nodes are deactivated automatically after seven days with no activity \(no new facts, catalog, or reports\).

## Install a license key

Install the `license.key` file to upgrade from a test installation to an active installation.

### About this task

### Procedure

1.  Install the license key by copying the file to `/etc/puppetlabs/license.key`.

    -   For monolithic installations, add the file to the master node.

    -   For split installations, add the file to the master and console nodes.


## Verify installed licenses and active nodes

Check the number of active nodes in your deployment, the number of licenses, and the expiration date for your license.

### Procedure

1.  Use the command line or console to view licensing details for your environment.

    -   On the master, run `puppet license`.
    -   In the console, click **License**.
    **Tip:** The console caches license information, so changes to your license key file might not appear in the console for up to 24 hours. To refresh the cache immediately, manually restart pe-console-services.


