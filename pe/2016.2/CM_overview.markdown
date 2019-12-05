---
layout: default
title: "Monitor and analyze infrastructure state"
canonical: "/pe/latest/CM_state_overview.html"

---

## Introducing configuration management

Configuration management leverages the data created and collected by Puppet Enterprise (PE) and provides infrastructure insight. The PE console offers the following configuration management tools:

* **[Overview](./CM_overview.html#node-run-statuses)** --- Displays the status of each of your nodes after the most recent Puppet run so you can quickly find issues and diagnose their causes.
* **[Node graph](./CM_graph.html)** --- Provides a graphic representation of any node's configuration so you can quickly spot the resources that are involved in issues. Visualizing relationships between resources also helps you organize them, creating more reliable deployments.
* **[Events](./CM_events.html)** --- Enables you to drill into specific class, node, and resource events so you can find out what caused them to fail, change, or run as no-op.
* **[Reports](./CM_reports.html)** --- Provides reports on node activity so you can audit your system and perform root cause analysis over time.

## Using the Overview page

When nodes fetch their configurations from the Puppet master, they send back inventory data and a report of their run. This information is captured at a high level on the **Overview** page in the [PE console](./console_accessing.html), where you can gather essential information about your nodes at a glance, such as how many nodes your Puppet master is managing, and when the page was last refreshed with data from PuppetDB.

![overview][overview]

[overview]: ./images/console/cm_overview.png

### Node run statuses

The Overview page provides summary information on the run statuses of your nodes following a Puppet run. The nodes are sorted into six possible run statuses:

#### Nodes in enforcement

* <img src="./images/console/icon_failed.png" alt="Failed changes icon" height="20" width="20" style="box-shadow:none;border:0;"> **With failed changes:** During its last run, Puppet encountered at least one problem that prevented it from making changes, either while evaluating whether the node was in the compliant state or while attempting to put the node into the compliant state. The error is usually tied to a particular resource (such as a file) managed by Puppet on the node. The node as a whole might still be functioning normally. The problem might even be caused by a situation not on the node but on the Puppet master, preventing the Puppet agent that's running on the node from verifying whether the node is compliant.

* <img src="./images/console/icon_changed.png" alt="Successful changes icon" height="20" width="20" style="box-shadow:none;border:0;"> **With successful changes:** This node's last Puppet run was successful, and changes were made to bring the node into compliance with its intended state.

  * **Intended catalog failed:** During the last Puppet run, the intended catalog for this node failed, so Puppet substituted a cached catalog. The cached catalog run was successful, and changes were made to the node. *This substatus will appear in the dashboard only in the event of a catalog failure.*

* <img src="./images/console/icon_unchanged.png" alt="No changes icon" height="20" width="20" style="box-shadow:none;border:0;"> **With no changes:** This node's last Puppet run was successful, and it was fully compliant; no changes were necessary.

  * **Intended catalog failed:** During the last Puppet run, the intended catalog for this node failed, so Puppet substituted a cached catalog. The cached catalog run was successful, and no changes were necessary. *This substatus will appear in the dashboard only in the event of a catalog failure.*

#### Nodes with no-op resources

* <img src="./images/console/icon_noop.png" alt="No-op success icon" height="20" width="20" style="box-shadow:none;border:0;"> **Would have successful changes:** During its last Puppet run, the `noop` metaparameter on one or more resources on this node was set to `true`. This setting prevents Puppet from running this node in enforcement. Instead, changes were simulated that would have been made to the node, were it in enforcement. See the node's last report for more details.

#### Nodes not reporting

* <img src="./images/console/icon_notreporting.png" alt="Not reporting icon" height="20" width="20" style="box-shadow:none;border:0;"> **Have not reported in 1+ hours:** The node hasn't reported to the Puppet master recently; something may be wrong. The cutoff for considering a node unresponsive defaults to one hour, and [can be configured](./console_config.html#changing-the-unresponsive-node-timeframe).

* <img src="./images/console/icon_notreporting.png" alt="Not reporting icon" height="20" width="20" style="box-shadow:none;border:0;"> **Have no reports:** Although Puppet Server is aware of this node's existence, the node has never submitted a Puppet report for one or more of the following reasons: it's a newly commissioned node; it has never come online; or its copy of Puppet is not configured correctly.

To report on, share, and analyze the data in another tool, export the node information to a CSV file by clicking **Export data**.

>**Note**: Expired or deactivated nodes are displayed in the Overview page for only seven days. If you want to extend the amount of time that you can view or search for these nodes, change PuppetDB's [`node-ttl` setting]({{puppetdb}}/configure.html#node-ttl). Be aware that changing this setting affects resources and exported resources.

### Filtering nodes

You can filter the list of nodes displayed in the Overview leaderboard by run status and by fact. 

**The filters you set are persistent.** If you set run status or fact filters, they continue to be applied to the Overview until they're changed or removed, even if you navigate to other pages in the console or log out of PE. The persistent storage impacts your browser, not your user account, and the cache can be removed by clearing browser data for the PE domain. 

Keep the following in mind as you work with filters on the Overview page:

* If you set a run status filter, and then you set a filter by fact, the Overview page takes both filters into account, and returns a list of only those nodes matching both filters.
* Clicking **Remove filter** removes **all** filters currently in effect.
* Filters persist when you leave and return to the page, even if you hide the fact value filter.
* Your browser can store filter settings even if you uninstall and reinstall PE. If you don't see a node you've added, try removing all filters.

#### Filter by run status

Focus the results of your latest Puppet run by selecting a run status in the leaderboard area.

#### Filter using facts

You can examine nodes in more detail by filtering by [node facts]({{facter}}/core_facts.html). For example, you can check that nodes you've updated have successfully changed, or find out what OS or IP address a set of failed nodes is running to better understand the failure. You might also filter by facts to fulfill an auditor's request for information, such as the number of nodes running a new version of software.

**To filter by node fact:**

1. Click **Filter by fact value** to open the fact filter.
2. Select whether nodes must have **all** or **any** of the fact values.
3. In the **Fact** box, select one of the available facts.
4. Select an **Operator**:
  * <b>=</b> &mdash; is
  * <b>!=</b> &mdash; is not
  * <b>~</b> &mdash; matches regex
  * <b>!~</b> &mdash; does not match regex
  * <b>></b> &mdash; greater than
  * <b>>=</b> &mdash; greater than or equal to
  * <b>&#60;</b> &mdash; less than
  * <b><=</b> &mdash; less than or equal to 
5. In the **Value** box, type in a value. Strings are case-sensitive, so make sure you use the correct case.

> **Tips for working with operators:** 
>
>  * The numeric operators <b>></b>, <b>>=</b>, <b>&#60;</b>, and <b><=</b> can be used only with facts that have a numeric value. 
>  * To match highly specific node facts, use <b>~</b> or <b>!~</b> with a regular expression for <b>Value</b>. 
>  * Select <b>~</b> as the operator to use wildcards and other regular expressions if you want to find matching facts without having to specify the exact value.

To see the facts and values reported by a node on its most recent run, click the node name and select **Node detail** from the drop-down. You will be redirected to the node's **Facts** tab, where you can view node details such as the operating system (`operatingsystem`), the amount of memory (`memorysize_mb`), and the primary IP address (`ipaddress`).

### A radiator view of node run status counts

The radiator provides a high-visibility overview of your infrastructure's nodes, and allows you to gather the following information at a glance:

* Count of all nodes actively managed by Puppet.
* Graphical display of run statuses.
* Time of last update.

Click **Radiator** at the top right of the **Overview** page to open this view.

