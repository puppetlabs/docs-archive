---
layout: default
title: "Infrastructure reports"
canonical: "/pe/latest/CM_reports.html"

---

Every Puppet run generates a report that provides information on that run, such as when the run took place, the nodes that were run, and the activity of resources --- fundamental units of a system, such as a specific service or package --- in the node. The reported metrics for resources can be any of the following:

* **Failed:** Number of resources that failed.
* **Changed:** Number of resources that changed.
* **Unchanged:** Number of resources that remained unchanged.
* **No-op:** Number of resources that would have been changed if not run in the no-op mode.
* **Skipped:** Number of resources that were skipped because they depended on resources that failed.
* **Failed restarts:** Number of resources that were supposed to restart but didn’t. For example, if changes to a resource notify a separate resource to restart, and that resource doesn’t restart, a failed restart is reported. It’s an indirect failure that occurred in a resource that was otherwise unchanged.
* **Config retrieval:** Time spent retrieving the catalog for the node (in seconds).
* **Run time:** Time spent applying the catalog on the node (in seconds).

![reports][reports]

[reports]: ./images/console/burnside_cm_reports.png

Filter the reports by status using the filter drop-down at the top of the **Reports** page. Click **Filter by fact value** to display the fact value filter, which allows you to further refine your report searches. 

You can also inspect a node's details by clicking the node name in the Reports table. This takes you to the node's **Facts** tab in the Node Management section of the console. In this section, you can also examine other details of the node, such as classes, variables, reports, groups, and activity.

### Logs and resource events

To examine the report in greater detail, click the **Reported at** timestamp in the report table. This opens a page that provides details for the node’s resources in two sections: **Log** and **Events**.

The **Log** tab provides errors, warnings, or notifications about what happened during the node's latest Puppet run. 


Each message has one of the following labels:

> **Standard labels**
>
> * debug
> * info
> * notice
>
> **Caution label** _(shown in yellow)_
>
> * warning
>
> **Warning labels** _(shown in red)_
> 
> * err 
> * alert
> * emerg
> * crit

The caution and warning labels use highly saturated colors to support color blindness accessibility. 

![reportlabel][reportlabel]

[reportlabel]: ./images/console/burnside_cm_reportlabel.png

The table items can be sorted by ascending or descending status label or time of run, so that the report can be read chronologically or in order of message level severity.

The **Events** tab lists each resource on the node, its status, and --- if it has changed --- what it has changed from and what it has changed to. For example, a user or a file might change from absent to present. Use the filter drop-down at the top of the page to filter resources by status. 

You can sort resource events by utilizing the controls at the top of the **Time by type** and **Resource type** columns, or filter results by selecting any result in either of these columns. The results of the sorting and filtering actions performed with the left column populate into the right column, where they can also be sorted by **Resource** or **Status**.

If you click the caret next to a changed resource in the list, run details are provided directly below the resource name. The run details describe what happened to the resource during the Puppet run.


