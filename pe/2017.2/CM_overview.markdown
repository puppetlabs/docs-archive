---
layout: default
title: "Monitor current infrastructure state"
canonical: "/pe/latest/CM_state_overview.html"

---

When nodes fetch their configurations from the Puppet master, they send back inventory data and a report of their run. This information is summarized on the **Overview** page in the Puppet Enterprise (PE) console. 

The Overview page displays the most recent run status of each of your nodes so you can quickly find issues and diagnose their causes. You can also use this page to gather essential information about your infrastructure at a glance, such as how many nodes your Puppet master is managing, and whether any nodes are unresponsive.

<!--Reference-->
## Node run statuses

The Overview page displays the run status of each node following the most recent Puppet run. There are 10 possible run statuses.  

### Nodes run in enforcement mode

* <img src="./images/console/icon_failed.png" alt="Failed changes icon" height="20" width="20" style="box-shadow:none;border:0;"> **With failures:** This node’s last Puppet run failed, or Puppet encountered an error that prevented it from making changes. 

   Investigate these failures and fix the cause. The error is usually tied to a particular resource (such as a file) managed by Puppet on the node. The node as a whole might still be functioning normally. Alternatively, the problem might be caused by a situation on the Puppet master, preventing the node's agent from verifying whether the node is compliant.

* <img src="./images/console/icon_corrective.png" alt="Corrective changes icon" height="20" width="20" style="box-shadow:none;border:0;"> **With corrective changes:** During the last Puppet run, Puppet found inconsistencies between the last applied catalog and this node’s configuration, and corrected those inconsistencies to match the catalog.

   *Corrective change reporting is only available on agent nodes running PE 2016.4 and later. Agents running earlier versions will report all change events as "with intentional changes".*

* <img src="./images/console/icon_changed.png" alt="Intentional changes icon" height="20" width="20" style="box-shadow:none;border:0;"> **With intentional changes:** During the last Puppet run, changes to the catalog were successfully applied to the node.

* <img src="./images/console/icon_unchanged.png" alt="No changes icon" height="20" width="20" style="box-shadow:none;border:0;"> **Unchanged:** This node's last Puppet run was successful, and it was fully compliant. No changes were necessary.

### Nodes run in no-op mode

   *No-op mode reporting is only available on agent nodes running PE 2016.4 and later. Agents running earlier version will report all no-op mode runs as "would be unchanged".*

* <img src="./images/console/icon_failed.png" alt="Failed changes icon" height="20" width="20" style="box-shadow:none;border:0;"> **With failures:** This node’s last Puppet run in no-op mode failed, or Puppet encountered an error that prevented it from simulating changes.

* <img src="./images/console/icon_corrective.png" alt="Corrective changes icon" height="20" width="20" style="box-shadow:none;border:0;"> **Would have corrective changes:** During the last Puppet run, Puppet found inconsistencies between the last applied catalog and this node’s configuration, and would have corrected those inconsistencies to match the catalog.

* <img src="./images/console/icon_changed.png" alt="Intentional changes icon" height="20" width="20" style="box-shadow:none;border:0;"> **Would have intentional changes:** During the last Puppet run, catalog changes would have been applied to the node.

* <img src="./images/console/icon_unchanged.png" alt="No changes icon" height="20" width="20" style="box-shadow:none;border:0;"> **Would be unchanged:** This node’s last Puppet run was successful, and the node was fully compliant. No changes would have been necessary.

### Nodes not reporting

* <img src="./images/console/icon_notreporting.png" alt="Not reporting icon" height="20" width="20" style="box-shadow:none;border:0;"> **Unresponsive for 1+ hours:** The node hasn't reported to the Puppet master recently. Something might be wrong. The cutoff for considering a node unresponsive defaults to one hour, and [can be configured](./console_config.html#changing-the-unresponsive-node-timeframe).

   Check the run status table to see the timestamp for the last known Puppet run for the node and an indication of whether its last known run was in no-op mode. Correct the problem to resume Puppet runs on the node.

* <img src="./images/console/icon_notreporting.png" alt="Not reporting icon" height="20" width="20" style="box-shadow:none;border:0;"> **Have no reports:** Although Puppet Server is aware of this node's existence, the node has never submitted a Puppet report for one or more of the following reasons: it's a newly commissioned node; it has never come online; or its copy of Puppet is not configured correctly.

>**Note**: Expired or deactivated nodes are displayed on the Overview page for seven days. To extend the amount of time that you can view or search for these nodes, change PuppetDB's [`node-ttl` setting]({{puppetdb}}/configure.html#node-ttl). Changing this setting affects resources and exported resources.

### Special categories

In addition to reporting the run status of each node, the Overview page provides a secondary count of nodes that fall into the following special categories:

* **Intended catalog failed:** During the last Puppet run, the intended catalog for this node failed, so Puppet substituted a cached catalog, as per your configuration settings. This typically occurs when you have compilation errors in your Puppet code. Check the [Puppet run's log](./CM_reports.html#logs-and-resource-events) for details. *This category is shown only if one or more agents fail to retrieve a valid catalog from Puppet Server.*

* **Enforced resources found:** During the last Puppet run in no-op mode, one or more resources was enforced, as per your use of the `noop => false` metaparameter setting. *This category is shown only if enforced resources are present on one or more nodes.*

<!--Reference--> 
## How Puppet determines node run statuses

Puppet uses a hierarchical system to determine a single run status for each node. This system gives higher priority to the activity types most likely to cause problems in your deployment, so you can focus on the nodes and events most in need of attention.

During a Puppet run, several activity types might occur on a single node. A node's run status reflects the activity with the highest alert level, regardless of how many events of each type took place during the run. Failure events receive the highest alert level, and no change events receive the lowest.

| Run status | Definitely happened | Might also have happened |
| :--------: | ------------------- | ------------------ |
| <img src="./images/console/icon_failed.png" alt="Failed changes icon" height="20" width="20" style="box-shadow:none;border:0;"> | Failure | Corrective change, intentional change, no change |
| <img src="./images/console/icon_corrective.png" alt="Corrective changes icon" height="20" width="20" style="box-shadow:none;border:0;"> | Corrective change | Intentional change, no change |
| <img src="./images/console/icon_changed.png" alt="Intentional changes icon" height="20" width="20" style="box-shadow:none;border:0;"> | Intentional change | No change |
| <img src="./images/console/icon_unchanged.png" alt="Unchanged icon" height="20" width="20" style="box-shadow:none;border:0;"> | No change |  |

For example, during a Puppet run in enforcement mode, a node with 100 resources receives intentional changes on 30 resources, corrective changes on 10 resources, and no changes on the remaining 60 resources. This node's run status is "with corrective changes."

Node run statuses also prioritize run mode (either enforcement or no-op) over the state of individual resources. This means that a node run in no-op mode is _always_ reported in the **Nodes run in no-op** column, even if some of its resource changes were enforced. Suppose the no-op flags on a node's resources are all set to false. Changes to the resources are enforced, not simulated. Even so, because it is run in no-op mode, the node's run status is "would have intentional changes."

<!--Concept-->
## Filtering nodes on the Overview page

You can filter the list of nodes displayed in the run status table on the Overview page by run status and by node fact. If you set a run status filter, and also set a node fact filter, the table takes both filters into account, and shows only those nodes matching both filters.

Clicking **Remove filter** removes **all** filters currently in effect.

**Important:** **The filters you set are persistent.** If you set run status or fact filters on the Overview page, they continue to be applied to the table until they're changed or removed, even if you navigate to other pages in the console or log out of PE. The persistent storage is associated with the browser tab, not your user account, and is cleared when you close the tab.

<!--Task-->
## Filter by node run status

The status counts section at the top of the Overview page shows a summary of the number of nodes with each run status as of the last Puppet run. Filter nodes by run status to quickly focus on nodes with failures or change events. 

1. In the status counts section, select a run status (such as **with corrective changes** or **have no reports**) or a run status category (such as **Nodes run in no-op**). 

<!--Task-->
## Filter by node fact

You can create a highly specific list of nodes for further investigation by using the fact filter tool. 

For example, you can check that nodes you've updated have successfully changed, or find out the operating systems or IP addresses of a set of failed nodes to better understand the failure. You might also filter by facts to fulfill an auditor's request for information, such as the number of nodes running a particular version of software.

1. Click **Filter by fact value**. In the **Fact** field, select one of the available facts. An empty fact field is not allowed.  
   
   **Tip:** To see the facts and values reported by a node on its most recent run, click the node name in the run status table, then select the node’s **Facts** tab. 

1. Select an **Operator**:

   Operator | Meaning | Notes
   :---: | --- | ---
   <b>=</b> | is | 
   <b>!=</b> | is not | 
   <b>~</b> | matches a regular expression (regex) | Select this operator to use wildcards and other regular expressions if you want to find matching facts without having to specify the exact value.
   <b>!~</b> | does not match a regular expression (regex) | 
   <b>></b> | greater than | Can be used only with facts that have a numeric value.
   <b>>=</b> | greater than or equal to | Can be used only with facts that have a numeric value.
   <b>&#60;</b> | less than | Can be used only with facts that have a numeric value.
   <b><=</b> | less than or equal to | Can be used only with facts that have a numeric value. 
  
1. In the **Value** field, enter a value. Strings are case-sensitive, so make sure you use the correct case. 

   The filter will display an error if you use an invalid string operator (for example, selecting a numeric value operator such as <b>>=</b> and entering a non-numeric string such as "pilsen" as the value) or enter an invalid regular expression. 
   
   **Note:** If you enter an invalid or empty value in the **Value** field, PE takes the following action in order to avoid a filter error:
    * Invalid or empty Boolean facts are processed as *false*, and results are retrieved accordingly.
    * Invalid or empty numeric facts are processed as *0*, and results are retrieved accordingly.
    * Invalid or incomplete regular expressions invalidate the filter, and no results are retrieved.
   
1. Click **Add**.

1. As needed, repeat these steps to add additional filters. If filtering by more than one node fact, specify either **Nodes must match all rules** or **Nodes may match any rule**.

