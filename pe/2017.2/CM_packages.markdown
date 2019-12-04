---
layout: default
title: "Viewing all packages in use"
canonical: "/pe/latest/CM_packages.html"

---

The **Packages** page in the PE console shows all packages in use across your infrastructure by name, version, and provider, as well as the number of nodes using each package version. Use the Packages page to quickly identify which nodes are impacted by packages you know are eligible for maintenance updates, security patches, and license renewals. 

Package inventory reporting is available for all nodes with Puppet agent version 1.6.0 or later installed, including systems that are not actively managed by Puppet. 

> **Note:** Packages are gathered from all [available providers]({{puppet}}/types/package.html#package-providers). The package data reported on the Packages page can also be obtained by using the `puppet resource` command to search for `package`. Be aware that packages are only reported for the active system version of a given package management tool. For example, JRuby packages installed with `puppetserver gem install` are not displayed on the Packages page. 

## Enable package data collection

Package data collection is disabled by default, so the Packages page in the console will initially appear blank. In order to view a node's current package inventory, enable package data collection.

You can choose to collect package data on all your nodes, or just a subset. Any node with Puppet agent version 1.6.0 or later installed can report package data, including nodes that are not under active management with Puppet Enterprise. 

1. In the PE console, click **Classification**, then click **Add group**.

2. Create a [classification node group](./console_classes_groups.html) that contains the nodes you wish to collect package data from, and click **Add**. 

3. Click the name of your new node group. On the **Rules** screen, create a rule to [dynamically add the relevant nodes to your node group](./console_classes_groups.html#adding-nodes-dynamically). 

4. Click **Add rule**, and commit changes. 

5. Click **Classes**. In the **Add new class** field, select `puppet_enterprise::profile::agent` and click **Add class**. 

6. In the `puppet_enterprise::profile::agent` class, add:
    
    - **Parameter** -- `package_inventory_enabled`
    - **Value** -- `true`<br>
    
    Click **Add parameter**, and commit changes.

7. [Run Puppet](./console_classes_groups_running_puppet.html) to apply these changes to the nodes in your node group. 

   Puppet will enable package inventory collection on this Puppet run, and will begin collecting package data and reporting it on the Packages page on each subsequent Puppet run. 

8. Run Puppet a second time to begin collecting package data, then click **Packages** to view your package inventory. 

## Collect, filter, and export package data 

Use the Packages page to view an inventory of all packages on your systems, focus on packages in need of updates, and generate lists of impacted nodes.  

Before you begin, make sure you have [enabled package data collection](#enable-package-data-collection) for the nodes you wish to view. 

1. [Run Puppet](./console_classes_groups_running_puppet.html) to collect the latest package data from your nodes. 

2. Click **Packages** to view your package inventory. 

   **Note:** Packages are sorted alphabetically, first by name, then by version, then provider. Each unique package name is shown in boldface, with the name of additional versions of the same package shown in regular type.     

3. Hover over an individual package name and click **Export** to generate a CSV-formatted list of the nodes a particular package is installed on. 

4. To narrow the list of packages, enter the name of a package in the **Filter by package name** field and click **Apply**. 
   
   You can also filter by a part of a package name. Currently, filtering by version or provider is not supported.  

## View package data collection metadata

The **puppet_inventory_metadata** fact reports whether package data collection is enabled on a node, and shows the time spent collecting package data on the node during the last Puppet run.

Before you begin, make sure you have [enabled package data collection](#enable-package-data-collection) for the nodes you wish to view.

1. In the PE console, click **Classification** and click the name of the node group you created when enabling package data collection. 

2. Click **Matching nodes** and select a node from the list.

3. On the node's inventory page, click **Facts** and locate **puppet_inventory_metadata** in the list. The fact value will look something like: 

   ```
   {
     "packages" : {
       "collection_enabled" : true,
       "last_collection_time" : "1.9149s"
     }
   }
   ```

## Disable package data collection

If you need to disable package data collection, set `package_inventory_enabled` to `false` and run Puppet twice. 

1. Click **Classification** and select the node group you created when enabling package data collection.  

2. Click the **Classes** tab.

3. In the `puppet_enterprise::profile::agent` class, locate `package_inventory_enabled` and click **Edit**.

4. Change the **Value** of `package_inventory_enabled` to `false`, then commit changes.  
   
5. [Run Puppet](./console_classes_groups_running_puppet.html) to apply these changes to the nodes in your node group and disable package data collection. 

   **Note:** Package data will be collected for a final time during this run. 

6. Run Puppet a second time to purge package data from the impacted nodes' storage. 






