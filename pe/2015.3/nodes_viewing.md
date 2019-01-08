---
layout: default
title: "Viewing Node-Specific Information"
canonical: "/pe/latest/nodes_viewing.html"
---

Each node in a PE deployment has its own node details page in the PE console. This is where you can view details specific to an individual node, such as the node facts. To view the node-specific information, in **Nodes**, click **Inventory** and then click the specific node that you would like to view.

> **Note:** To export a list of all of the nodes being managed by Puppet to a CSV file, click **Export data** at the top right of the **Inventory** page.

## Viewing Node Facts

The **Facts** tab contains all of the fact values reported by the node on its most recent run. Facts include things like the operating system (`operatingsystem`), the amount of memory (`memorytotal`), and the primary IP address (`ipaddress`). You can also [add custom facts](/facter/latest/custom_facts.html) to your Puppet modules, and they too will show up in the inventory.

![facts tab](./images/console/facts_tab.png)

## Viewing Classes

The **Classes** tab shows the classes that have been assigned to the node. These classes have been assigned because the node matches the rules set in a node group containing the classes, or because the node was pinned to a node group containing the classes. **Source Group** shows the node group through which the class was applied.

## Viewing Variables

The **Variables** tab shows a list of variables that have been applied to the node. These variables have been applied to the node because they have been set for node groups that the node currently matches. **Source Group** shows the node group in which the variable is set.


## Viewing Node Reports

When nodes fetch their configurations from the Puppet master, they send back a report of their Puppet run. The **Reports** tab shows a list of reports for the node, where each report represents a single Puppet run.

### Understanding the Resource Sync State

During a Puppet run, Puppet compares the _current state_ of each resource to the _desired state_ for that resource. If Puppet successfully compares them and the resource is already in sync (the current state is the desired state), Puppet moves on to the next resource without changing anything. Otherwise, it will attempt to modify the resource. The change to resources on each Puppet run is summarized in the columns that are displayed in the **Reports** tab.

* **Total:** The total number of resources managed by the Puppet run.
* **Failed:** The number of resources that were out of sync and Puppet tried to change but was unsuccessful.
* **Changed:** The number of resources that were out of sync and were changed by Puppet to reach the desired state.
* **Unchanged:** The number of resources that were fully compliant and did not require any changes.
* **No-op:** The number of resources that would have been changed, but were not actually changed because Puppet was instructed not to make changes on this resource (either with the `--noop` command-line option, the `noop` setting, or the `noop => true` metaparameter). Puppet simulates changes to these resources instead of enforcing them.
* **Skipped:** The number of resources that did not meet a prerequisite. In this case, Puppet does not compare the current state to the desired state. (This prerequisite is either a failure in one of the resource's dependencies or a timing limitation set with [the `schedule` metaparameter](/references/4.3.latest/metaparameter.html#schedule).)
* **Failed restarts:** The number of resources that could not be restarted.
* **Config retrieval:** How long it took to retrieve the node's configuration.
* **Runtime:** How long it took for the Puppet run to complete.

Clicking a report in the **Reports** tab takes you to **Configuration Management** where you can view more detailed information about the Puppet run.

### Exporting Node Reports

To export a list of node reports to a CSV file, click **Export data** at the top right of the reports table. This will export all reports for the given node that have not yet expired.

To configure the reports expiration time, see the [PuppetDB documentation](/puppetdb/3.2/configure.html#report-ttl). The default expiration is 14 days.

## Viewing Node Groups

The **Groups** tab shows a list of groups that the node currently matches. The node matches a group when it matches a rule that has been specified in that group.

The **Parent group** column shows the parent of the group listed in the **Group** column. A group inherits classification data from all ancestor groups. For more information on inheritance, see [How Does Inheritance Work?](./console_classes_groups_inheritance.html)

The **Environment** column shows the environment that has been selected for the group. This doesn't necessarily indicate the environment that the node is in. Rather, it is the environment that is used to control which classes are available for selection in that group. In most cases, the environment that has been set for the node group should be the same as the environment that the matching nodes are in. This column is a good place to confirm that you have set the correct environments. To learn more about working with environments in the console, see [Grouping and Classifying Nodes](./console_classes_groups.html).

