# Viewing and managing all packages in use

The Packages page in the console shows all packages in use across your infrastructure by name, version, and provider, as well as the number of instances of each package version in your infrastructure. Use the Packages page to quickly identify which nodes are impacted by packages you know are eligible for maintenance updates, security patches, and license renewals.

Package inventory reporting is available for all nodes with Puppet agent version 1.6.0 or later installed, including systems that are not actively managed by Puppet.

**Tip:** Packages are gathered from all available providers. The package data reported on the Packages page can also be obtained by using the `puppet resource` command to search for `package`.

## Enable package data collection

Package data collection is disabled by default, so the Packages page in the console will initially appear blank. In order to view a node's current package inventory, enable package data collection.

### About this task

You can choose to collect package data on all your nodes, or just a subset. Any node with Puppet agent version 1.6.0 or later installed can report package data, including nodes that are not under active management with Puppet Enterprise.

### Procedure

1.  In the console, click Classification.

    -   If you want to collect package data on all your nodes, click the **PE Agent** node group.

    -   If you want to collect package data on a subset of your nodes, click **Add group** and create a new classification node group. Select **PE Agent** as the group's parent name. Once the new node group is set up, use the **Rules** tab to dynamically add the relevant nodes.

2.  Click **Configuration**. In the **Add new class** field, select **puppet\_enterprise::profile::agent** and click **Add class**.

3.  In the **puppet\_enterprise::profile::agent** class, set the **Parameter** to **package\_inventory\_enabled** and the **Value** to true. Click **Add parameter**, and commit changes.

4.  Run Puppet to apply these changes to the nodes in your node group.

    Puppet will enable package inventory collection on this Puppet run, and will begin collecting package data and reporting it on the Packages page on each subsequent Puppet run.

5.  Run Puppet a second time to begin collecting package data, then click Packages.


## View and manage package inventory

To view and manage the complete inventory of packages on your systems, use the Packages page in the console.

### Before you begin

Make sure you have enabled package data collection for the nodes you wish to view.

### Procedure

1.  Run Puppet to collect the latest package data from your nodes.

2.  In the console, click Packages to view your package inventory. To narrow the list of packages, enter the name or partial name of a package in the **Filter by package name** field and click **Apply**.

3.  Click any package name or version to enter the detail page for that package.

4.  On a package's detail page, use the **Version** selector to locate nodes with a particular package version installed.

5.  Use the **Instances** selector to locate nodes where the package is not managed with Puppet, or to view nodes on which a package instance is managed with Puppet.

    To quickly find the place in your manifest where a Puppet-managed package is declared, select a code path in the **Instances** selector and click **Copy path**.

6.  To modify a package on a group of nodes:

    -   If the package is managed with Puppet, select a code path in the **Instances** selector and click **Copy path**, then navigate to and update the manifest.

    -   If the package is not managed with Puppet, click **Run** \> **Task** and create a new task.


## View package data collection metadata

The `puppet_inventory_metadata` fact reports whether package data collection is enabled on a node, and shows the time spent collecting package data on the node during the last Puppet run.

### Before you begin

Make sure you have enabled package data collection for the nodes you wish to view.

### Procedure

1.  Click Classification and select the node group you created when enabling package data collection.

2.  Click **Matching nodes** and select a node from the list.

3.  On the node's inventory page, click **Facts** and locate **puppet\_inventory\_metadata** in the list.

    The fact value will look something like:

    ```
    {
         "packages" : {
           "collection_enabled" : true,
           "last_collection_time" : "1.9149s"
         }
       }
    ```


## Disable package data collection

If you need to disable package data collection, set **package\_inventory\_enabled** to `false` and run Puppet twice.

### About this task

### Procedure

1.  Click Classification and select the node group you used when enabling package data collection.

2.  Click **Configuration**.

3.  In the **puppet\_enterprise::profile::agent** class, locate **package\_inventory\_enabled** and click **Edit**.

4.  Change the **Value** of **package\_inventory\_enabled** to `false`, then commit changes.

5.  Run Puppet to apply these changes to the nodes in your node group and disable package data collection.

    Package data will be collected for a final time during this run.

6.  Run Puppet a second time to purge package data from the impacted nodes' storage.


### Results

