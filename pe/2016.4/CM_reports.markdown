---
layout: default
title: "Infrastructure reports"
canonical: "/pe/latest/CM_reports.html"

---

Every Puppet run generates a report that provides information on that run, such as when the run took place, the nodes that were run, and the activity of resources --- fundamental units of a system, such as a specific service or package --- in the node. These reports are collected on the **Reports** page in the PE console.

## Working with the Reports page

![reports][reports]

[reports]: ./images/console/cm_reports.png

Each report lists the number of resources on the node in each of the following states:

* **Correction applied:** Number of resources that received a corrective change after Puppet identified resources that were out of sync with the applied catalog.
* **Failed:** Number of resources that failed.
* **Changed:** Number of resources that changed.
* **Unchanged:** Number of resources that remained unchanged.
* **No-op:** Number of resources that would have been changed if not run in no-op mode.
* **Skipped:** Number of resources that were skipped because they depended on resources that failed.
* **Failed restarts:** Number of resources that were supposed to restart but didn’t. For example, if changes to one resource notify another resource to restart, and that resource doesn’t restart, a failed restart is reported. It’s an indirect failure that occurred in a resource that was otherwise unchanged.

Each report also contains the following information:

* **No-op mode:** An indicator of whether the node was run in no-op mode.
* **Config retrieval:** Time spent retrieving the catalog for the node (in seconds).
* **Run time:** Time spent applying the catalog on the node (in seconds).

### Filtering reports

Filter the reports by run status using the filter drop-down at the top of the **Reports** page. Click **Filter by fact value** to display the fact value filter, which allows you to further refine your report searches.

**The filters you set are persistent.** If you set fact value filters on the **Reports** page, they continue to be applied to the **Reports** page until they're changed or removed, even if you navigate to other pages in the console or log out of PE. The persistent storage is associated with the browser tab, not your user account, and is cleared when you close your tab.

**To filter by node fact:**

1. Click **Filter by fact value** to open the fact filter.
2. Select whether nodes must have **all** or **any** of the fact values.
3. In the **Fact** box, select one of the available facts.
4. Select an **Operator**:
  * <b>=</b> &mdash; is
  * <b>!=</b> &mdash; is not
  * <b>~</b> &mdash; matches a regular expression (regex)
  * <b>!~</b> &mdash; does not match a regular expression (regex)
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

### Viewing node details

Inspect a node's details by clicking the node name in the Reports table. This takes you to the node's **Facts** tab in the Node Management section of the console. In this section, you can also examine other details of the node, such as classes, variables, reports, groups, and activity.

## Logs and resource events

To examine the report in greater detail, click the **Reported at** timestamp in the report table. This opens a page that provides details for the node’s resources in two sections: **Log** and **Events**.

### Log tab

The **Log** tab provides errors, warnings, and notifications about what happened during the node's latest Puppet run.

![reportlabel][reportlabel]

[reportlabel]: ./images/console/cm_reportlabel.png

Each message has one of the following labels:

> **Standard labels**
>
> * debug
> * info
> * notice
>
> **Caution labels** _(shown in yellow)_
>
> * warning
> * alert
>
> **Warning labels** _(shown in red)_
>
> * err
> * emerg
> * crit

Sort the table by ascending or descending status label or time of run by clicking on the column heading. This lets you read the report chronologically or in order of message severity.

### Events tab

The **Events** tab lists each resource on the node, its status, whether correction was applied to the resource, and --- if it changed --- what it changed from and what it changed to. For example, a user or a file might change from absent to present. Use the filter drop-down at the top of the page to filter resources by event status.

![eventstab][eventstab]

[eventstab]: ./images/console/cm_eventstab.png

Sort resource events by clicking the **Time by type** and **Resource type** column controls. Filter the table by clicking any result in either of these columns. When you sort and filter in the left column, it changes what is shown in the right column, where you can also sort by **Resource**, **Correction applied**, or **Event status**.

Click the arrow to the left of a changed resource name to see run details, which describe what happened to the resource during the Puppet run.


