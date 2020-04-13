# Monitoring current infrastructure state

When nodes fetch their configurations from the Puppet master, they send back inventory data and a report of their run. This information is summarized on the Overview page in the console.

The Overview page displays the most recent run status of each of your nodes so you can quickly find issues and diagnose their causes. You can also use this page to gather essential information about your infrastructure at a glance, such as how many nodes your Puppet master is managing, and whether any nodes are unresponsive.

## Node run statuses

The Overview page displays the run status of each node following the most recent Puppet run. There are 10 possible run statuses.

### Nodes run in enforcement mode

-   **![Red exclamation point icon](icon_failed.png) With failures**

    This node’s last Puppet run failed, or Puppet encountered an error that prevented it from making changes.

    The error is usually tied to a particular resource \(such as a file\) managed by Puppet on the node. The node as a whole might still be functioning normally. Alternatively, the problem might be caused by a situation on the Puppet master, preventing the node's agent from verifying whether the node is compliant.

-   **![Icon that looks like half of an apple pie](icon_corrective.png) With corrective changes**

    During the last Puppet run, Puppet found inconsistencies between the last applied catalog and this node’s configuration, and corrected those inconsistencies to match the catalog.

    **Note:** Corrective change reporting is available only on agent nodes running PE 2016.4 and later. Agents running earlier versions report all change events as "with intentional changes."

-   **![Icon that looks like half of a blueberry pie](icon_changed.png) With intentional changes**

    During the last Puppet run, changes to the catalog were successfully applied to the node.

-   **![Green checkmark icon](icon_unchanged.png) Unchanged**

    This node's last Puppet run was successful, and it was fully compliant. No changes were necessary.


### Nodes run in no-op mode

**Note:** No-op mode reporting is available only on agent nodes running PE 2016.4 and later. Agents running earlier versions report all no-op mode runs as "would be unchanged."

-   **![Red exclamation point icon](icon_failed.png) With failures**

    This node’s last Puppet run in no-op mode failed, or Puppet encountered an error that prevented it from simulating changes.

-   **![Icon that looks like half of an apple pie](icon_corrective.png) Would have corrective changes**

    During the last Puppet run, Puppet found inconsistencies between the last applied catalog and this node’s configuration, and would have corrected those inconsistencies to match the catalog.

-   **![Icon that looks like half of a blueberry pie](icon_changed.png) Would have intentional changes**

    During the last Puppet run, catalog changes would have been applied to the node.

-   **![Green checkmark icon](icon_unchanged.png) Would be unchanged**

    This node’s last Puppet run was successful, and the node was fully compliant. No changes would have been necessary.


### Nodes not reporting

-   **![Gray dash icon](icon_notreporting.png) Unresponsive**

    The node hasn't reported to the Puppet master recently. Something might be wrong. The cutoff for considering a node unresponsive defaults to one hour, and can be configured.

    Check the run status table to see the timestamp for the last known Puppet run for the node and an indication of whether its last known run was in no-op mode. Correct the problem to resume Puppet runs on the node.

-   **![Gray dash icon](icon_notreporting.png) Have no reports**

    Although Puppet Server is aware of this node's existence, the node has never submitted a Puppet report for one or more of the following reasons: it's a newly commissioned node; it has never come online; or its copy of Puppet is not configured correctly.

    **Note:** Expired or deactivated nodes are displayed on the Overview page for seven days. To extend the amount of time that you can view or search for these nodes, change the `node-ttl` setting in PuppetDB. Changing this setting affects resources and exported resources.


### Special categories

In addition to reporting the run status of each node, the Overview page provides a secondary count of nodes that fall into special categories.

-   **Intended catalog failed**

    During the last Puppet run, the intended catalog for this node failed, so Puppet substituted a cached catalog, as per your configuration settings.

    This typically occurs when you have compilation errors in your Puppet code. Check the Puppet run's log for details.

    This category is shown only if one or more agents fail to retrieve a valid catalog from Puppet Server.

-   **Enforced resources found**

    During the last Puppet run in no-op mode, one or more resources was enforced, as per your use of the `noop => false` metaparameter setting.

    This category is shown only if enforced resources are present on one or more nodes.


### How Puppet determines node run statuses

Puppet uses a hierarchical system to determine a single run status for each node. This system gives higher priority to the activity types most likely to cause problems in your deployment, so you can focus on the nodes and events most in need of attention.

During a Puppet run, several activity types might occur on a single node. A node's run status reflects the activity with the highest alert level, regardless of how many events of each type took place during the run. Failure events receive the highest alert level, and no change events receive the lowest.

|Run status|Definitely happened|Might also have happened|
|:--------:|-------------------|------------------------|
|![Red exclamation point icon](icon_failed.png)|Failure|Corrective change, intentional change, no change|
|![Icon that looks like half of an apple pie](icon_corrective.png)|Corrective change|Intentional change, no change|
|![Icon that looks like half of a blueberry pie](icon_changed.png)|Intentional change|No change|
|![Green checkmark icon](icon_unchanged.png)|No change| |

For example, during a Puppet run in enforcement mode, a node with 100 resources receives intentional changes on 30 resources, corrective changes on 10 resources, and no changes on the remaining 60 resources. This node's run status is "with corrective changes."

Node run statuses also prioritize run mode \(either enforcement or no-op\) over the state of individual resources. This means that a node run in no-op mode is always reported in the **Nodes run in no-op** column, even if some of its resource changes were enforced. Suppose the no-op flags on a node's resources are all set to false. Changes to the resources are enforced, not simulated. Even so, because it is run in no-op mode, the node's run status is "would have intentional changes."

## Filtering nodes on the Overview page

You can filter the list of nodes displayed on the Overview page by run status and by node fact. If you set a run status filter, and also set a node fact filter, the table takes both filters into account, and shows only those nodes matching both filters.

Clicking **Remove filter** removes all filters currently in effect.

The filters you set are persistent. If you set run status or fact filters on the Overview page, they continue to be applied to the table until they're changed or removed, even if you navigate to other pages in the console or log out. The persistent storage is associated with the browser tab, not your user account, and is cleared when you close the tab.

**Important:** The filter results count and the fact filter matching nodes counts are cached for two minutes after first retrieval. This reduces the total load on PuppetDB and decreases page load time, especially for fact filters with multiple rows. As a result, the displayed counts might be up to two minutes out of date.

### Filter by node run status

The status counts section at the top of the Overview page shows a summary of the number of nodes with each run status as of the last Puppet run. Filter nodes by run status to quickly focus on nodes with failures or change events.

#### Procedure

1.  In the status counts section, select a run status \(such as **with corrective changes** or **have no reports**\) or a run status category \(such as **Nodes run in no-op**\).


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

5.  As needed, repeat these steps to add additional filters. If filtering by more than one node fact, specify either **Nodes must match all rules** or **Nodes can match any rule**.


#### Results

## Filtering nodes in your node list

Filter your node list by node name or by PQL query to more easily inspect them.

### Filter your node list by node name

Filter your nodes list by node name to inspect them as a group.

#### About this task

#### Procedure

1.  Select **Node name**, type in the word you want to filter by, and click **Submit**.


### Filter your nodes by PQL query

Filter your nodes list using a common PQL query.

#### About this task

Filtering your nodes list by PQL query enables you to manage them by specific factors, such as by operating system, report status, or class.

#### Procedure

1.  Specify a target by doing one of the following:

    -   Enter a query that selects the target you want. See the [Puppet Query Language \(PQL\) reference](https://puppet.com/docs/puppetdb/latest/api/query/v4/pql.html) for more information.
    -   Click **Common queries**. Select one of the queries and replace the defaults in the braces \(`{ }`\) with values that specify the target you want.

        |Target|PQL query|
        |------|---------|
        |All nodes|`nodes[certname] { }`|
        |Nodes with a specific resource \(example: httpd\)|`resources[certname] { type = "Service" and title = "httpd" }`|
        |Nodes with a specific fact and value \(example: OS name is CentOS\)|`inventory[certname] { facts.os.name = "<OS>" }`|
        |Nodes with a specific report status \(example: last run failed\)|`reports[certname] { latest_report_status = "failed" }`|
        |Nodes with a specific class \(example: Apache\)|`resources[certname] { type = "Class" and title = "Apache" }`|
        |Nodes assigned to a specific environment \(example: production\)|`nodes[certname] { catalog_environment = "production" }`|
        |Nodes with a specific version of a resource type \(example: OpenSSL is v1.1.0e\)|`resources[certname] {type = "Package" and title="openssl" and parameters.ensure = "1.0.1e-51.el7_2.7" }`|
        |Nodes with a specific resource and operating system \(example: httpd and CentOS\)|`inventory[certname] { facts.operatingsystem = "CentOS" and resources { type = "Service" and title = "httpd" } }`|


## Monitor PE services

Puppet Enterprise includes tools for monitoring the status of core services including the activity, classifier, and RBAC services, Puppet Server, and PuppetDB. You can monitor these services on the command line, and from within the console.

### View the Puppet Services status monitor

The Puppet Services status monitor provides a visual overview of the current state of key services, and can be used to quickly determine whether an unresponsive or restarting service is causing an issue with your deployment.

#### Procedure

1.  In the console, click Overview.

2.  Click **Puppet Services status** to open the monitor.


#### Results

A checkmark appears next to **Puppet Services status** if all applicable services are accepting requests. In the event that no data is available, a question mark appears next to the link. If one or more services is restarting or not accepting requests, a warning icon appears.

### `puppet infrastructure status` command

The `puppet infrastructure status` command displays errors and alerts from PE services, including the activity, classifier, and RBAC services, Puppet Server, and PuppetDB.

The command reports separately on the master and any compilers or replicas in your environment. You must run the command as root.

