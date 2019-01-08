---
layout: default
title: "Monitor and Analyze Infrastructure State on the Overview Page"
canonical: "/pe/latest/CM_state_overview.html"

---

## Introducing Configuration Management

Configuration Management leverages the data created and collected by Puppet Enterprise (PE) and provides insights into your infrastructure in the following ways:

* **[Overview](./CM_overview.html#overview-of-node-states)** --- Reports the state of your nodes after the most recent Puppet run so you can quickly find issues and diagnose the cause.
* **[Node Graph](./CM_overview.html#view-the-node-graph)** --- Provides a graphic representation of your node's configuration so you can quickly spot the resources that are involved in issues. Visualizing relationships between resources helps you organize them and create more reliable deployments.
* **[Events](./CM_events.html)** --- Enables you to drill into specific class, node, and resource events so you can find out what caused them to fail, change, skip, or run as no-op.
* **[Reports](./CM_reports.html)** --- Provides reports on node activity so you can audit your system and perform root cause analysis over time.


## Overview of Node States

When nodes fetch their configurations from the Puppet master, they send back inventory data and a report of their run. This information is captured at a high level on the **Overview** page in the console, where you can learn some essential information about your nodes at a glance, such as how many nodes your Puppet master is managing, and when the Overview page was last refreshed with the data from PuppetDB.

![overview][overview]

[overview]: ./images/console/cm_overview.png

## Node States

Overview also provides information on how many nodes are in each of six possible states after a Puppet run. You can filter the information by selecting a state to view only those nodes in a particular state. The state overview table links to the run's report. You can also investigate nodes that have failed, changed, skipped, or run as no-op on the **Events** page. The six possible node states on the **Overview** page are the following (note that three of the states share the same icon):

* <span style="font-family: Helvetica, Arial, Verdana; color: #D93129;">Failed:</span> ![failed][failed] During its last run, Puppet encountered at least one problem either while evaluating whether the node was in the compliant state or while attempting to put the node into the compliant state. Either way, the error is usually tied to a particular resource (such as a file) managed by Puppet on the node. The node as a whole might still be functioning normally. The problem might even be caused by a situation not on the node but on the Puppet master, preventing the Puppet agent that's running on the node from verifying whether the node is compliant.

* <span style="font-family: Helvetica, Arial, Verdana; color: #78B7D2;">Changed:</span> ![changed][changed] This node's last Puppet run was successful, and changes were made to bring the node into compliance.

* <span style="font-family: Helvetica, Arial, Verdana; color: #78C145;">Unchanged:</span> ![unchanged][unchanged] This node's last Puppet run was successful, and it was fully compliant; no changes were necessary.

* <span style="font-family: Helvetica, Arial, Verdana; color: #818285;">No-op:</span> ![noop][noop] During its last Puppet run, this node would have made changes, but because  it either was running in no-op mode or found a discrepancy in a resource whose `noop` metaparameter was set to `true`, it simulated the changes instead of enforcing them. See the node's last report for more details.

* <span style="font-family: Helvetica, Arial, Verdana; color: #818285;">Unresponsive:</span> ![noop][noop] The node hasn't reported to the Puppet master recently; something may be wrong. The cutoff for considering a node unresponsive defaults to one hour, and can be configured in the `console.conf` file at `/etc/puppetlabs/console-services/conf.d` by adding this code:

		console: {
		...
  			no-longer-reporting-cutoff: 3600
		...
		}

* <span style="font-family: Helvetica, Arial, Verdana; color: #818285;">Unreported:</span> ![noop][noop] Although the Puppet server is aware of this node's existence, the node has never submitted a Puppet report for any of the following reasons: it's  a newly-commissioned node; it's never come online; or its copy of Puppet is not configured correctly.

To report on, share, and analyze the data in another tool, export the node information to a CSV file using the **Export data** button: ![export][export]

>**Note**: Expired or deactivated nodes are displayed in the Overview page for only 7 days. If you want to extend the amount of time that you can view or search for these nodes, change PuppetDB's [`node-ttl` setting](/puppetdb/latest/configure.html#node-ttl) setting. Be aware, though, that changing this setting affects resources and exported resources.


[export]: ./images/console/icon_csvdownload.png
[changed]: ./images/console/icon_changed.png
[failed]: ./images/console/icon_failed.png
[unchanged]: ./images/console/icon_unchanged.png
[noop]: ./images/console/icon_noop.png


## Filtering Nodes

You can filter the list of nodes displayed in the Overview table by run status and by fact.

The filters you set in the filter area are persistent. If you set run status or fact filters, they continue to be applied to the Overview until they're changed or removed, even if you navigate to other Configuration or Nodes pages. The persistent storage is with your browser, not your user account, and the cache can be removed by clearing browser data for the PE domain. It's good to know that the persistence can lead to the following behavior:

* If you set a run status filter, and then you set a filter by fact, the **Nodes matching all filters** number takes both filters into account.
* When you click **Remove all filters**, **Filter by run status** is also reset to the default setting of **all**.
* Filters persist when you leave and return to the page, even if you hide the fact value filter.
* Your browser can store filter settings even if you uninstall and reinstall PE. If you aren't seeing a node you've added, try removing all filters.

### Filter By Run Status

Select the status that you want to view in the **Filter by run status** box to focus the results of your latest Puppet run.

### Filter Using Facts

You can examine node states in more detail by filtering by [node facts](/facter/2.3/core_facts.html). For example, you can check that nodes you've updated have successfully changed, or find out what OS or IP address a set of failed nodes is running to better understand the failure. You might also filter by facts to fulfill an auditor's request for information on such things as number of nodes running a new version of software.

**To filter by node fact**

1. Click **Filter by fact value** to open the fact filter.
2. In the **Filter by run status** box, select the state you want to view.
3. Select whether nodes must have **all** or **any** of the fact values.
4. In the **Fact** box, select one of the available facts.
5. Select an **Operator** and then in the **Value** box, type in a value. Strings are case-sensitive, so make sure you use the correct case.

To see the facts and values reported by a node on its most recent run, click the node name and then in the **Node Management** page, click the **Facts** tab. The facts include things like the operating system (`operatingsystem`), the amount of memory (`memorysize_mb`), and the primary IP address (`ipaddress`).

>**Hint**:  Select "matches regex" as the operator to use wildcards and other regular expressions if you want to find matching facts without having to specify the exact value.

## View the Node Graph

Your nodes' catalogs describe the desired state for each resource that PE manages. The node graph provides a graph view of a node's catalog. Examine the graph to locate and solve problems by finding which resources are causing nodes to fail or which resources have changed. In addition, the graph enables you to visualize the relationship of resources across all configurations on your nodes. Assessing these relationships helps you create more reliable deployments.

To open the node graph for a node, click **View graph** for a specific node in the  Overview table.

![node_graph][node_graph]

[node_graph]: ./images/console/cm_nodegraph.png

The selected node's config version, timestamp, and last run status are displayed. In the **Hightlight** box, select **stage**, **class**, or **resource**. Then select the specific stage, class, or resource you want to view. The graph repositions to zoom in on the selected item and the detail pane opens.

Click **Failures** or **Changes** to dim non-failure or unchanged parts of the graph, and click **All** to return to regular view.

## A Radiator View of Node State Counts

While the node graph provides a detailed view of an individual node's states, the radiator provides a high-visibility overview of your infrastructure's node states that includes the following information at a glance:

* Count of all nodes actively managed by Puppet.
* Graphical display of run states.
* Time of last update.

Click **Radiator** at the top right of the page to open this view.
