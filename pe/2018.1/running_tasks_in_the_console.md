---
author: Isaac Eldridge <isaac.eldridge@puppet.com\>
---

# Running tasks from the console

PE gives you the ability to execute ad-hoc tasks on target machines from the console. Task are the quickest way to upgrade packages, restart services, or perform any other type of single-action executions on your nodes.

When you set up a job to run a task from the console, the orchestrator creates a job ID to track the job, shows you all nodes included in the job, and runs the tasks on those nodes in the appropriate order. Puppet compiles a new catalog for each node included in the job. There are three ways to specify the job target \(the nodes you want to run jobs on\):

-   A Puppet Query Language \(PQL\) query

-   A static node list

-   A node group


You can't combine these methods, and if you switch from one to the other with the **Inventory** list, the target list clears and starts over. In other words, if you create a target list by using a node list, switching to a PQL query clears the node list, and vice versa. You can do a one-time conversion of a PQL query to a static node list if you want to add or remove nodes from the query results.

**Important:** Running a task does not update your Puppet configuration. If you run a task that changes the state of a resource that Puppet is managing \(such as upgrading a service or package\), a subsequent Puppet run will change the state of that resource back to what is defined in your Puppet configuration. For example, if you use a task to update the version of a managed package, the version of that package will be reset to whatever is specified in the relevant manifest on the next Puppet run.

## Run a task on a PQL query

Create a PQL query to run tasks on nodes that meet specific conditions.

### Before you begin

Install the tasks you want to use.

Make sure you have access to the nodes you want to target.

Make sure you have permissions to run tasks and PQL queries.

### About this task

### Procedure

1.  In the console, in the Run section, click **Task**.

2.  In the **Task** field, select a task to run, for example `service`.

    **Note:** If the tasks you expect are not available, you either have no tasks installed, or you don't have the correct permissions to run them.

3.  In the **Job description** field, provide a description that will be shown on the job list and job details pages.

4.  Set any parameters and values for the task. Click **Add parameter** for each parameter-value pair you add.

    **Note:** Parameters marked with an asterisk \(`*`\) are required. Optional parameters are available in a dropdown. If there are no required or optional parameters, you can add arbitrary parameters provided those parameters are allowed in the task.

    Express values as strings, arrays, objects, integers, or booleans \(true or false\). You must express empty strings as two double quotes with no space \(`""`\). Wrap boolean values in double quotes \(for example, `"false"`\).

    If you chose the `service` task, then you have two required parameters. For **action**, you can choose `restart`. For service, enter nginx.

5.  To schedule the job to run at a future time, select **Later** and choose a start date and time.

6.  In the **Inventory** list, select **PQL query**.

7.  Specify a target by doing one of the following:

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

8.  Click **Submit query** and click **Refresh** to update the node results.

9.  If you change or edit the query after it runs, click **Submit query** again.

10. To convert the PQL query target results to a node list, for use as a node list target, click **Convert query to static node list**.

    **Note:** If you select this option, the job target becomes a node list. You can add or remove nodes from the node list before running the job, but you cannot edit the query.

11. Run or schedule the job.

    -   **Run job**
    -   **Schedule job**

### Results

**Important:** When you run this job, the PQL query will run again, and the job may run on a different set of nodes than what is currently displayed. If you want the job to run only on the list as displayed, convert the query to a static node list before you run the job.

## Run a task on a node list

Create a node list target when you need to run a task on a specific set of nodes that isn't easily defined by a PQL query.

### Before you begin

Install the tasks you want to use.

Make sure you have access to the nodes you want to target.

Make sure you have permissions to run tasks and PQL queries.

### Procedure

1.  In the console, in the Run section, click **Task**.

2.  In the **Job description** field, provide a description that will be shown on the job list and job details pages.

3.  In the **Task** field, select a task to run, for example `service`.

    **Note:** If the tasks you expect are not available, you either have no tasks installed, or you don't have the correct permissions to run them.

4.  Set any parameters and values for the task. Click **Add parameter** for each parameter-value pair you add.

    **Note:** Parameters marked with an asterisk \(`*`\) are required. Optional parameters are available in a dropdown. If there are no required or optional parameters, you can add arbitrary parameters provided those parameters are allowed in the task.

    Express values as strings, arrays, objects, integers, or booleans \(true or false\). You must express empty strings as two double quotes with no space \(`""`\). Wrap boolean values in double quotes \(for example, `"false"`\).

    If you chose the `service` task, then you have two required parameters. For **action**, you can choose `restart`. For service, enter nginx.

5.  To schedule the job to run at a future time, select **Later** and choose a start date and time.

6.  In the **Inventory** list, select **Node list**.

7.  To create a node list, in the search field, start typing in the names of nodes to search for, and click **Search**.

    **Note:** The search does not handle regular expressions.

8.  Select the nodes you want to add to the job. You can select nodes from multiple searches to create the node list target.

9.  To remove any nodes from the target, select them and click **Remove selected**, or first click **Select all** and then click **Remove selected**.

10. Run or schedule the job.

    -   **Run job**
    -   **Schedule job**

### Results

## Run a task on a node group

Similar to running a task on a list of nodes that you create in the console, you can run a task on a node group.

### Before you begin

Install the tasks you want to use.

Make sure you have access to the nodes you want to target.

### About this task

**Note:** If you do not have permissions to view a node group, or the node group doesn't have any matching nodes, that node group won't be listed as an option for viewing. In addition, a node group will not appear if no rules have been specified for it.

### Procedure

1.  In the console, in the Run section, click **Task**.

2.  In the **Task** field, select a task to run, for example `service`.

    **Note:** If the tasks you expect are not available, you either have no tasks installed, or you don't have the correct permissions to run them.

3.  In the **Job description** field, provide a description that will be shown on the job list and job details pages.

4.  Set any parameters and values for the task. Click **Add parameter** for each parameter-value pair you add.

    **Note:** Parameters marked with an asterisk \(`*`\) are required. Optional parameters are available in a dropdown. If there are no required or optional parameters, you can add arbitrary parameters provided those parameters are allowed in the task.

    Express values as strings, arrays, objects, integers, or booleans \(true or false\). You must express empty strings as two double quotes with no space \(`""`\). Wrap boolean values in double quotes \(for example, `"false"`\).

    If you chose the `service` task, then you have two required parameters. For **action**, you can choose `restart`. For service, enter nginx.

5.  To schedule the job to run at a future time, select **Later** and choose a start date and time.

6.  In the **Inventory** list, select **Node group**.

7.  In the **Choose a node group** box, type or select a node group, and click **Select**.

8.  Run or schedule the job.

    -   **Run job**
    -   **Schedule job**

## Schedule a task

Schedule a job to run a task at a particular date and time.

### Before you begin

Install the tasks you want to use.

Make sure you have access to the nodes you want to target.

### About this task

### Procedure

1.  In the console, in the Run section, click **Task**.

2.  In the **Job description** field, provide a description that will be shown on the job list and job details pages.

3.  In the **Task** field, select a task to run, for example `service`.

    **Note:** If the tasks you expect are not available, you either have no tasks installed, or you don't have the correct permissions to run them.

4.  Set any parameters and values for the task. Click **Add parameter** for each parameter-value pair you add.

    **Note:** Parameters marked with an asterisk \(`*`\) are required. Optional parameters are available in a dropdown. If there are no required or optional parameters, you can add arbitrary parameters provided those parameters are allowed in the task.

    Express values as strings, arrays, objects, integers, or booleans \(true or false\). You must express empty strings as two double quotes with no space \(`""`\). Wrap boolean values in double quotes \(for example, `"false"`\).

    If you chose the `service` task, then you have two required parameters. For **action**, you can choose `restart`. For service, enter nginx.

5.  Under **Schedule**, select **Later** and choose a start date, time, and time zone for the job to run.

    **Tip:** Based on the configuration of your console, the default time zone is either UTC, Coordinated Universal Time, or the local time, UTC offset, of your browser.

6.  From the list of target types, select the category of nodes you want to target.

    -   **Node list** Add individual nodes by name.
    -   **PQL Query** Use the Puppet query language to retrieve a list of nodes.
    -   **Node group** Select an existing node group.
7.  Click **Schedule job**.

    Your task appears on the **Schedule** tab of the **Jobs** page.


**Related information**  


[Delete a scheduled job](running_tasks_in_the_console.md#)

[Review jobs from the console](reviewing_jobs_in_the_console.md#)

## Stop a job in progress

You can stop a job if, for example, you realize you need to reconfigure a class or push a configuration change that the job needs.

### About this task

When you stop an on-demand Puppet run, runs that are underway will finish, and runs that have not started will not start.

Stopping tasks depends on how many nodes are targeted in the job versus the concurrency limit you've set. If the concurrency limit is higher than the number of nodes targeted, all nodes will complete the task, as those nodes will have already started the task.

### Procedure

1.  To stop a job:

    -   In the console, go to the **Job details** page and select the **Puppet run** or **Task** tab. From the list of jobs, find the one you want and click **Stop**.
    -   On the command line, press **CTRL + C**.

## Delete a scheduled job

Delete a job that is scheduled to run at a later time.

### About this task

**Tip:** You must have the appropriate role-based permissions to delete another user's scheduled job.

### Procedure

1.  In the console, go to **Jobs** \> **Scheduled** tab.

2.  From the list of jobs, find the one you want to delete and click **Remove**.


