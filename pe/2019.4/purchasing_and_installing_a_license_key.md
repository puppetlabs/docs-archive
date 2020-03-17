# Purchasing and installing a license key

Your license must support the number of nodes that you want to manage with Puppet Enterprise.

## Complimentary license

You can manage up to 10 nodes at no charge, and no license key is needed. When you have 11 or more active nodes and no license key, license warnings appear in the console until you install an appropriate license key.

## Purchased license

To manage 11 or more active nodes, you must purchase a license. After you purchase a license and install a license key file, your licensed node count and subscription expiration date appear on the **License** page.

**Note:** To support spikes in node usage, four days per calendar month you can exceed your licensed node count up to double the number of nodes you purchased. This increased number of nodes is called your *bursting limit*. You must buy more nodes for your license if you exceed the licensed node count within the bursting limit on more than four days per calendar month, or if you exceed your bursting limit at all. In these cases, license warnings appear in the console until you contact your Puppet representative.

**Parent topic:**[Installing](installing.md)

**Related information**  


[How nodes are counted](node_count.md)

## Getting a license

Contact Puppet to purchase a license subscription.

-   New PE customers — Purchase a subscription from the Puppet website, or contact our sales team. Find more information at [Ready to buy Puppet Enterprise?](https://buy.puppet.com/)

-   Existing PE customers — To add nodes, upgrade, or renew your annual license, contact your sales representative or email sales@puppet.com.


**Tip:** To reduce your active node count and free up licenses, remove inactive nodes from your deployment. By default, nodes with Puppet agents are automatically deactivated after seven days with no activity \(no new facts, catalog, or reports\).

**Related information**  


[Remove agent nodes](adding_and_removing_nodes.md#)

## Install a license key

Install the `license.key` file to upgrade from a test installation to an active installation.

1.  Install the license key by copying the file to `/etc/puppetlabs/license.key` on the master node.

2.  Verify that Puppet has permission to read the license key by checking its ownership and permissions: `ls -la /etc/puppetlabs/license.key`

3.  If the ownership is not `root` and permissions are not `-rw-r--r--` \(octal 644\), set them:

    ```
    sudo chown root:root /etc/puppetlabs/license.key
    sudo chmod 644 /etc/puppetlabs/license.key
    ```


## View license details for your environment

Check the number of active nodes in your deployment, the number of licensed nodes you purchased, and the expiration date for your license.

-   In the console, click **License**.


The **License** page opens with information on your licensed nodes, bursting limit, and subscription expiration date. Any license warnings that appear in the console navigation are explained here. For example, if your license is expired or out of compliance.

**Related information**  


[Remove agent nodes](adding_and_removing_nodes.md#)

[Puppet orchestrator API: usage endpoint](orchestrator_api_usage_endpoint.md#)

