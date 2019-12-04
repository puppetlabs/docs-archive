---
layout: default
title: "Monitor and analyze infrastructure state"
canonical: "/pe/latest/CM_state_overview.html"

---

## Introducing configuration management

Configuration management leverages the data created and collected by Puppet Enterprise (PE) and provides infrastructure insight. The PE console offers the following configuration management tools:

* **[Overview](./CM_overview.html#working-with-the-overview-page)** --- Displays the status of each of your nodes after the most recent Puppet run so you can quickly find issues and diagnose their causes.
* **[Node graph](./CM_graph.html)** --- Provides a graphic representation of any node's configuration so you can spot the resources that are involved in issues. Visualizing relationships between resources also helps you organize them, creating more reliable deployments.
* **[Events](./CM_events.html)** --- Enables you to drill into specific class, node, and resource events so you can find out what caused them to fail, change, or run as no-op.
* **[Reports](./CM_reports.html)** --- Provides reports on node activity so you can audit your system and perform root cause analysis over time.

## Working with the Overview page

When nodes fetch their configurations from the Puppet master, they send back inventory data and a report of their run. This information is summarized on the **Overview** page in the [PE console](./console_accessing.html), where you can gather essential information about your nodes at a glance, such as how many nodes your Puppet master is managing, and when the page was last refreshed with data from PuppetDB.

![overview][overview]

[overview]: ./images/console/cm_overview.png

View a summary of the number of nodes in each run status in the status counts section of the Overview page. Use the run status table below the status counts to see additional information about each node, including the node's name, the timestamp of the last report, and whether the last run was in no-op mode.

### Node run statuses

The Overview page provides information on the run statuses of your nodes following a Puppet run. Each node is assigned one of 10 possible run statuses:

#### Nodes run in enforcement mode

* <img src="./images/console/icon_failed.png" alt="Failed changes icon" height="20" width="20" style="box-shadow:none;border:0;"> **With failures:** This node’s last Puppet run failed, or Puppet encountered an error that prevented it from making changes.

   Investigate these failures and fix the cause. The error is usually tied to a particular resource (such as a file) managed by Puppet on the node. The node as a whole might still be functioning normally. Alternatively, the problem might be caused by a situation on the Puppet master, preventing the node's agent from verifying whether the node is compliant.

* <img src="./images/console/icon_corrective.png" alt="Corrective changes icon" height="20" width="20" style="box-shadow:none;border:0;"> **With corrective changes:** During the last Puppet run, Puppet found inconsistencies between the last applied catalog and this node’s configuration, and corrected those inconsistencies to match the catalog.

   *Corrective change reporting is only available on agent nodes running PE 2016.4 or later. Agents running earlier versions will report all change events as "with intentional changes".*

* <img src="./images/console/icon_changed.png" alt="Intentional changes icon" height="20" width="20" style="box-shadow:none;border:0;"> **With intentional changes:** During the last Puppet run, changes to the catalog were successfully applied to the node.

* <img src="./images/console/icon_unchanged.png" alt="No changes icon" height="20" width="20" style="box-shadow:none;border:0;"> **Unchanged:** This node's last Puppet run was successful, and it was fully compliant. No changes were necessary.

#### Nodes run in no-op mode

   *No-op mode reporting is only available on agent nodes running PE 2016.4 or later. Agents running earlier version will report all no-op mode runs as "would be unchanged".*

* <img src="./images/console/icon_failed.png" alt="Failed changes icon" height="20" width="20" style="box-shadow:none;border:0;"> **With failures:** This node’s last Puppet run in no-op mode failed, or Puppet encountered an error that prevented it from simulating changes.

* <img src="./images/console/icon_corrective.png" alt="Corrective changes icon" height="20" width="20" style="box-shadow:none;border:0;"> **Would have corrective changes:** During the last Puppet run, Puppet found inconsistencies between the last applied catalog and this node’s configuration, and would have corrected those inconsistencies to match the catalog.

* <img src="./images/console/icon_changed.png" alt="Intentional changes icon" height="20" width="20" style="box-shadow:none;border:0;"> **Would have intentional changes:** During the last Puppet run, catalog changes would have been applied to the node.

* <img src="./images/console/icon_unchanged.png" alt="No changes icon" height="20" width="20" style="box-shadow:none;border:0;"> **Would be unchanged:** This node’s last Puppet run was successful, and the node was fully compliant. No changes would have been necessary.

#### Nodes not reporting

* <img src="./images/console/icon_notreporting.png" alt="Not reporting icon" height="20" width="20" style="box-shadow:none;border:0;"> **Unresponsive for 1+ hours:** The node hasn't reported to the Puppet master recently. Something might be wrong. The cutoff for considering a node unresponsive defaults to one hour, and [can be configured](./console_config.html#changing-the-unresponsive-node-timeframe).

   Check the run status table, below, to see the timestamp for the last known run for the node and an indication of whether its last known run was in no-op mode. Correct the problem to resume Puppet runs on the node.

* <img src="./images/console/icon_notreporting.png" alt="Not reporting icon" height="20" width="20" style="box-shadow:none;border:0;"> **Have no reports:** Although Puppet Server is aware of this node's existence, the node has never submitted a Puppet report for one or more of the following reasons: it's a newly commissioned node; it has never come online; or its copy of Puppet is not configured correctly.

>**Note**: Expired or deactivated nodes are displayed on the Overview page for seven days. To extend the amount of time that you can view or search for these nodes, change PuppetDB's [`node-ttl` setting]({{puppetdb}}/configure.html#node-ttl). Changing this setting affects resources and exported resources.

#### Special categories

In addition to reporting the run status of each node, the Overview page provides a secondary count of nodes that fall into the following special categories:

* **Intended catalog failed:** During the last Puppet run, the intended catalog for this node failed, so Puppet substituted a cached catalog, as per your configuration settings. This typically occurs when you have compilation errors in your Puppet code. Check the [Puppet run's log](./CM_reports.html#logs-and-resource-events) for details. *This category is shown only if one or more agents fail to retrieve a valid catalog from Puppet Server.*

* **Enforced resources found:** During the last Puppet run in no-op mode, one or more resources was enforced, as per your use of the `noop => false` metaparameter setting. *This category is shown only if enforced resources are present on one or more nodes.*

### How Puppet determines node run statuses

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

### Filtering nodes

You can filter the list of nodes displayed in the run status table below the status counts by run status and by fact.

**The filters you set are persistent.** If you set run status or fact filters on the Overview page, they continue to be applied to the table until they're changed or removed, even if you navigate to other pages in the console or log out of PE. The persistent storage is associated with the browser tab, not your user account, and is cleared when you close the tab.

**Tips for working with filters:**

* If you set a run status filter, and then set a filter by fact, the table takes both filters into account, and shows only those nodes matching both filters.
* Clicking **Remove filter** removes **all** filters currently in effect.
* Filters persist when you leave and return to the page, even if you hide the fact value filter.
* Your browser tab stores filter settings even if you uninstall and reinstall PE. If you don't see a node you've added, try removing all filters.

#### Filter by run status

Focus the results of your latest Puppet run by selecting a run status in the status counts area.

#### Filter using facts

You can examine nodes in more detail by filtering by [node facts]({{facter}}/core_facts.html). For example, you can check that nodes you've updated have successfully changed, or find out the operating systems or IP addresses of a set of failed nodes to better understand the failure. You might also filter by facts to fulfill an auditor's request for information, such as the number of nodes running a particular version of software.

**To filter by node fact:**

1. Click **Filter by fact value** to open the fact filter.
2. Select whether nodes must have **all** or **any** of the fact values.
3. In the **Fact** field, select one of the available facts.
4. Select an **Operator**:
  * <b>=</b> &mdash; is
  * <b>!=</b> &mdash; is not
  * <b>~</b> &mdash; matches a regular expression (regex)
  * <b>!~</b> &mdash; does not match a regular expression (regex)
  * <b>></b> &mdash; greater than
  * <b>>=</b> &mdash; greater than or equal to
  * <b>&#60;</b> &mdash; less than
  * <b><=</b> &mdash; less than or equal to
5. In the **Value** field, enter a value. Strings are case-sensitive, so make sure you use the correct case.
6. Click **Add**.

> **Tips for working with the fact value filter:**
>
>  * The numeric operators <b>></b>, <b>>=</b>, <b>&#60;</b>, and <b><=</b> can be used only with facts that have a numeric value.
>  * To match highly specific node facts, use <b>~</b> or <b>!~</b> with a regular expression for <b>Value</b>.
>  * Select <b>~</b> as the operator to use wildcards and other regular expressions if you want to find matching facts without having to specify the exact value.
>  * If you enter an invalid or empty value in the **Value** field, PE takes the following action in order to avoid a filter error:
>    * **Invalid or empty Boolean facts** are processed as *false*, and results are retrieved accordingly.
>    * **Invalid or empty numeric facts** are processed as *0*, and results are retrieved accordingly.
>    * **Invalid or incomplete regular expressions** invalidate the filter, and no results are retrieved.

To see the facts and values reported by a node on its most recent run, click the node name and select **Node detail** from the drop-down. On the node's **Facts** tab, view details such as the operating system (`operatingsystem`), the amount of memory (`memorysize_mb`), and the primary IP address (`ipaddress`).

### A radiator view of node run status counts

The radiator view provides an overview of your infrastructure's nodes, and allows you to gather the following information at a glance:

* Count of all nodes actively managed by Puppet.
* Graphical display of run statuses.
* Time of last update.

Click **Radiator** at the top right of the **Overview** page to open this view.

