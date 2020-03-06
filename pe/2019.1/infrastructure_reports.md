# Infrastructure reports

Each time Puppet runs on a node, it generates a report that provides information such as when the run took place, any issues encountered during the run, and the activity of resources on the node. These reports are collected on the Reports page in the console.

## Working with the reports table

The Reports page provides a summary view of key data from each report. Use this page to track recent node activity so you can audit your system and perform root cause analysis over time.

The reports table lists the number of resources on each node in each of the following states:

|**Correction applied**|Number of resources that received a corrective change after Puppet identified resources that were out of sync with the applied catalog.|
|**Failed**|Number of resources that failed.|
|**Changed**|Number of resources that changed.|
|**Unchanged**|Number of resources that remained unchanged.|
|**No-op**|Number of resources that would have been changed if not run in no-op mode.|
|**Skipped**|Number of resources that were skipped because they depended on resources that failed.|
|**Failed restarts**|Number of resources that were supposed to restart but didn’t.

 For example, if changes to one resource notify another resource to restart, and that resource doesn’t restart, a failed restart is reported. It’s an indirect failure that occurred in a resource that was otherwise unchanged.

|

The reports table also offers the following information:

-   **No-op mode:** An indicator of whether the node was run in no-op mode.
-   **Config retrieval:** Time spent retrieving the catalog for the node \(in seconds\).
-   **Run time:** Time spent applying the catalog on the node \(in seconds\).

**Tip:** Report count caching is used to improve console performance. In some cases, caching might cause summary counts of available reports to be displayed inaccurately the first time the page is accessed after a fresh install.

## Filtering reports

You can filter the list of reports displayed on the Reports page by run status and by node fact. If you set a run status filter, and also set a node fact filter, the table takes both filters into account, and shows only those reports matching both filters.

Clicking **Remove filter** removes all filters currently in effect.

The filters you set are persistent. If you set run status or fact filters on the Reports page, they continue to be applied to the table until they're changed or removed, even if you navigate to other pages in the console or log out. The persistent storage is associated with the browser tab, not your user account, and is cleared when you close the tab.

### Filter by node run status

Filter reports to quickly focus on nodes with failures or change events by using the **Filter by run status** bar.

#### About this task

#### Procedure

1.  Select a run status \(such as **No-op mode: with failures**\). The table updates to reflect your filter selection.

2.  To remove the run status filter, select **All run statuses**.


### Filter by node fact

You can create a highly specific list of nodes for further investigation by using the fact filter tool.

#### About this task

For example, you can check that nodes you've updated have successfully changed, or find out the operating systems or IP addresses of a set of failed nodes to better understand the failure. You might also filter by facts to fulfill an auditor's request for information, such as the number of nodes running a particular version of software.

#### Procedure

1.  Click **Filter by fact value**. In the **Fact** field, select one of the available facts. An empty fact field is not allowed.

    **Tip:** To see the facts and values reported by a node on its most recent run, click the node name in the Run status table, then select the node’s **Facts** tab.

2.  Select an Operator:

    |Operator|Meaning|Notes|
    |:------:|-------|-----|
    |**=**|is| |
    |**!=**|is not| |
    |**~**|matches a regular expression \(regex\)|Select this operator to use wildcards and other regular expressions if you want to find matching facts without having to specify the exact value.|
    |**!~**|does not match a regular expression \(regex\)| |
    |**\>**|greater than|Can be used only with facts that have a numeric value.|
    |**\>=**|greater than or equal to|Can be used only with facts that have a numeric value.|
    |**<**|less than|Can be used only with facts that have a numeric value.|
    |**<=**|less than or equal to|Can be used only with facts that have a numeric value.|

3.  In the **Value** field, enter a value. Strings are case-sensitive, so make sure you use the correct case.

    The filter displays an error if you use an invalid string operator \(for example, selecting a numeric value operator such as ****\>=**** and entering a non-numeric string such as pilsen as the value\) or enter an invalid regular expression.

    **Note:** If you enter an invalid or empty value in the **Value** field, PE takes the following action in order to avoid a filter error:

    -   Invalid or empty Boolean facts are processed as **false**, and results are retrieved accordingly.

    -   Invalid or empty numeric facts are processed as **0**, and results are retrieved accordingly.

    -   Invalid or incomplete regular expressions invalidate the filter, and no results are retrieved.

4.  Click **Add**.

5.  As needed, repeat these steps to add additional filters. If filtering by more than one node fact, specify either **Nodes must match all rules** or **Nodes may match any rule**.


#### Results

## Working with individual reports

To examine a report in greater detail, click **Report time**. This opens a page that provides details for the node’s resources in three sections: **Events**, **Log**, and **Metrics**.

### Events

The **Events** tab lists the events for each managed resource on the node, its status, whether correction was applied to the resource, and — if it changed — what it changed from and what it changed to. For example, a user or a file might change from absent to present.

To filter resources by event type, click **Filter by event status** and choose an event.

Sort resources by name or events by severity level, ascending or descending, by clicking the **Resource** or **Events** sorting controls.

To download the events data as a `.csv` file, click **Export data**. The filename is `events-<node name>-<timestamp>`.

### Log

The **Log** tab lists errors, warnings, and notifications from the node's latest Puppet run.

Each message is assigned one of the following severity levels:

|Standard|Caution \(yellow\)|Warning \(red\)|
|--------|------------------|---------------|
|debug|warning|err|
|info|alert|emerg|
|notice| |crit|

To read the report chronologically, click the time sorting controls. To read it in order of issue severity, click the severity level sorting controls.

To download the log data as a `.csv` file, click **Export data**. The filename is `log-<node name>-<timestamp>`.

### Metrics

The **Metrics** tab provides a summary of the key data from the node's latest Puppet run.

|Metric|Description|
|------|-----------|
|Report submitted by:|The certname of the Puppet master that submitted the report to PuppetDB.|
|Puppet environment|The environment assigned to the node.|
|Puppet run|-   The time that the Puppet run began

-   The time that the Puppet master submitted the catalog

-   The time that the Puppet run finished

-   The time PuppetDB received the report

-   The duration of the Puppet run

-   The length of time to retrieve the catalog

-   The length of time to apply the resources to the catalog


|
|Catalog application|Information about the catalog application that produces the report: the config version that Puppet uses to match a specific catalog for a node to a specific Puppet run, the catalog UUID that identifies the catalog used to generate a report during a Puppet run, and whether the Puppet run used a cached catalog.|
|Resources|The total number of resources in the catalog.|
|Events|A list of event types and the total count for each one.|
|Top resource types|A list of the top resource types by time, in seconds, it took to be applied.|

