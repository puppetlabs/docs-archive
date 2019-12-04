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

    The filter will display an error if you use an invalid string operator \(for example, selecting a numeric value operator such as ****\>=**** and entering a non-numeric string such as pilsen as the value\) or enter an invalid regular expression.

    **Note:** If you enter an invalid or empty value in the **Value** field, PE takes the following action in order to avoid a filter error:

    -   Invalid or empty Boolean facts are processed as **false**, and results are retrieved accordingly.

    -   Invalid or empty numeric facts are processed as **0**, and results are retrieved accordingly.

    -   Invalid or incomplete regular expressions invalidate the filter, and no results are retrieved.

4.  Click **Add**.

5.  As needed, repeat these steps to add additional filters. If filtering by more than one node fact, specify either **Nodes must match all rules** or **Nodes may match any rule**.


#### Results

## Working with individual reports

To examine a report in greater detail, click the **Report time** timestamp. This opens a page that provides details for the node’s resources in two sections: **Log** and **Events**.

### Report log

The **Log** tab provides errors, warnings, and notifications about what happened during the node's latest Puppet run.

Each message has one of the following labels:

|Standard labels|Caution labels \(yellow\)|Warning labels \(red\)|
|---------------|-------------------------|----------------------|
|debug|warning|err|
|info|alert|emerg|
|notice| |crit|

Sort the table by status label or time by clicking on the sorting controls. This lets you read the report chronologically or in order of issue severity.

### Report events

The **Events** tab lists each resource on the node, its status, whether correction was applied to the resource, and — if it changed — what it changed from and what it changed to. For example, a user or a file might change from absent to present.

Use **Filter by event status** to filter resources by event type.

Sort resource events by clicking the **Run time \(sec\)** and **Resource type** sorting controls. Filter the table by clicking any result in either of these columns. When you sort and filter in the left column, it changes what is shown in the right column, where you can also sort by **Resource**, **Correction applied**, or **Event status**.

Click the arrow to the left of a changed resource name to see run details, which describe what happened to the resource during the Puppet run.

