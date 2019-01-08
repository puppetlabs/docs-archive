---
layout: default
title: " PE 2015.2 » Configuration Manager » Reports"
subtitle: "Infrastructure Reports"
canonical: "/pe/latest/CM_reports.html"

---

Every Puppet run generates a report that provides information on that run, such as when the run took place, the nodes that were run, and the activity of resources --- fundamental units of a system, such as a specific service or package --- in the node. The reported metrics for resources can be any of the following:

* Failed: Number of resources that failed.
* Changed: Number of resources that changed.
* Unchanged: Number of resources that remained unchanged.
* No-op: Number of resources that would have been changed if not run in the no-op mode.
* Skipped: Number of resources that were skipped because they depended on resources that failed.
* Failed restarts: Number of resources that were supposed to restart but didn’t. For example, if changes to a resource notify a separate resource to restart, and that resource doesn’t restart, it’s a failed restart. It’s an indirect failure that occurred in a resource that was otherwise unchanged.
* Config retrieval (in seconds): Time spent retrieving the catalog for the node.
* Run time (in seconds): Time spent applying the catalog on the node.

![reports][reports]

[reports]: ./images/console/cm_reports.png

You can filter the reports by status using the status tabs: Failed, Changed, Unchanged or No-op.

You can also inspect a node's details by clicking the node name in the Reports table. This takes you to the node's **Facts** tab in the Node Manager section. In this section, you can also examine other details of the node, such as classes, variables, reports, and groups.

### Events and Logs

To examine the report in greater detail, click the report timestamp in the report table. This opens a page that provides details for the node’s resources in two sections: Events and Log.

The **Events** tab lists each resource on the node, its status, and if it has changed, what it has changed from and what it has changed to. For example, a user or a file might change from absent to present. If you click a resource in the list, run details are provided directly below the resource name. The run details describe what happened to the resource during the Puppet run.

The **Log** tab provides errors, warnings, or notifications about the status of resources after a Puppet run. The messages have one of the following levels. Warnings and errors are color-coded as indicated. The others are not.

* debug
* info
* notice
* warning: yellow
* err: red
* alert
* emerg
* crit

The table items are listed in descending order according to time of run.

