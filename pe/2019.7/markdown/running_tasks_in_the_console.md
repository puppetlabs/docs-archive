---
author: Isaac Eldridge <isaac.eldridge@puppet.com\>
---

# Running tasks from the console

Run ad-hoc tasks on target machines to upgrade packages, restart services, or perform any other type of single-action executions on your nodes.

When you set up a job to run a task from the console, the orchestrator creates a job ID to track the job, shows you all nodes included in the job, and runs the tasks on those nodes in the appropriate order. Puppet compiles a new catalog for each node included in the job.

There are three ways to specify the job target \(the nodes you want to run tasks on\):

-   A static node list

-   A Puppet Query Language \(PQL\) query

-   A node group


You can't combine these methods, and if you switch from one to the other, the target list clears and starts over. In other words, if you create a target list by using a node list, switching to a PQL query clears the node list. You can do a one-time conversion of a PQL query to a static node list if you want to add or remove nodes from the query results.

**Important:** Running a task does not update your Puppet configuration. If you run a task that changes the state of a resource that Puppet is managing \(such as upgrading a service or package\), a subsequent Puppet run changes the state of that resource back to what is defined in your Puppet configuration. For example, if you use a task to update the version of a managed package, the version of that package is reset to whatever is specified in the relevant manifest on the next Puppet run.

## Run a task on a node list

Create a list of target nodes when you need to run a task on a specific set of nodes that isn't easily defined by a PQL query.

Install the tasks you want to use.

Make sure you have permission to run the tasks.

Make sure you have access to the nodes you want to target.

**Tip:** You can add network devices to a node list when you have installed modules for device transports in your production environment. You can find such modules in [Puppet Forge](https://forge.puppet.com/).

1.  In the console, in the **Orchestration** section, click **Tasks**.

2.  Click **Run a task** in the upper right corner of the **Tasks** page.

3.  In the **Code environment** field, select the environment where you installed the module containing the task you want to run. This defaults to `production`.

4.  In the **Task** field, select a task to run, for example `service`.

    **Note:** If the tasks you expect are not available, you either have no tasks installed, or you don't have the correct permissions to run them.

5.  In the **Job description** field, provide a description. The text you enter here appears on the job list and job details pages.

6.  Set any parameters and values for the task. Click **Add parameter** for each parameter-value pair you add.

    If you chose the `service` task, then you have two required parameters. For **action**, you can choose `restart`. For **service**, enter nginx.

    **Note:** Parameters marked with an asterisk \(`*`\) are required. Optional parameters are available in a dropdown. If there are no required or optional parameters, you can add arbitrary parameters provided those parameters are allowed in the task.

    Express values as strings, arrays, objects, integers, or booleans \(true or false\). You must express empty strings as two double quotes with no space \(`""`\). Wrap boolean values in double quotes \(for example, `"false"`\).

7.  Under **Schedule**, select **Later** and choose a start date, time, time zone, and frequency for the job to run.

    **Tip:** Based on the configuration of your console, the default time zone is either UTC, Coordinated Universal Time, or the local time, UTC offset, of your browser.

8.  From the list of target types, select **Node list**.

9.  Create the node list.

    1.  Expand the **Inventory nodes** target.

    2.  Enter the name of the node you want to find and click **Search**.

        **Note:** The search does not handle regular expressions.

    3.  From the list of results, select the nodes that you want to add to your list. They are added to a table below.

    4.  Repeat the search to add other nodes. You can select nodes from multiple searches to create the node list.

        **Tip:** To remove a node from the table, select the checkbox next to it and click **Remove selected**.

10. After you have selected all your target nodes, click **Run job** or **Schedule job**.

    Your task run appears on the **Tasks** page. Click the **Run \#** to view the results and output.


## Run a task over SSH

Use the SSH protocol to run tasks on target nodes that do not have the Puppet agent installed.

Install the tasks you want to use.

Make sure you have permission to run the tasks on all nodes.

1.  In the console, in the **Orchestration** section, click **Tasks**.

2.  Click **Run a task** in the upper right corner of the **Tasks** page.

3.  In the **Code environment** field, select the environment where you installed the module containing the task you want to run. This defaults to `production`.

4.  In the **Task** field, select a task to run, for example `service`.

    **Note:** If the tasks you expect are not available, you either have no tasks installed, or you don't have the correct permissions to run them.

5.  In the **Job description** field, provide a description. The text you enter here appears on the job list and job details pages.

6.  Set any parameters and values for the task. Click **Add parameter** for each parameter-value pair you add.

    If you chose the `service` task, then you have two required parameters. For **action**, you can choose `restart`. For **service**, enter nginx.

    **Note:** Parameters marked with an asterisk \(`*`\) are required. Optional parameters are available in a dropdown. If there are no required or optional parameters, you can add arbitrary parameters provided those parameters are allowed in the task.

    Express values as strings, arrays, objects, integers, or booleans \(true or false\). You must express empty strings as two double quotes with no space \(`""`\). Wrap boolean values in double quotes \(for example, `"false"`\).

7.  Under **Schedule**, select **Later** and choose a start date, time, time zone, and frequency for the job to run.

    **Tip:** Based on the configuration of your console, the default time zone is either UTC, Coordinated Universal Time, or the local time, UTC offset, of your browser.

8.  From the list of target types, select **Node list**.

9.  Create the node list.

    1.  Expand the **SSH nodes** target.

        **Note:** This target is available only for tasks permitted to run on all nodes.

    2.  Enter the target host names and the credentials required to access them. If you use an SSH key, include begin and end tags.

    3.  Select additional target options.

        For example, to add a target port number, select **Target port** from the drop-down list, enter the number, and click **Add**.

    4.  Click **Add nodes**. They are added to a table below.

    5.  Repeat these steps to add other nodes. You can add SSH nodes with different credentials to create the node list.

        **Tip:** To remove a node from the table, select the checkbox next to it and click **Remove selected**.

10. After you have selected all your target nodes, click **Run job** or **Schedule job**.

    Your task run appears on the **Tasks** page. Click the **Run \#** to view the results and output.


## Run a task over WinRM

Use the Windows Remote Management \(WinRM\) to run tasks on target nodes that do not have the Puppet agent installed.

Install the tasks you want to use.

Make sure you have permission to run the tasks on all nodes.

1.  In the console, in the **Orchestration** section, click **Tasks**.

2.  Click **Run a task** in the upper right corner of the **Tasks** page.

3.  In the **Code environment** field, select the environment where you installed the module containing the task you want to run. This defaults to `production`.

4.  In the **Task** field, select a task to run, for example `service`.

    **Note:** If the tasks you expect are not available, you either have no tasks installed, or you don't have the correct permissions to run them.

5.  In the **Job description** field, provide a description. The text you enter here appears on the job list and job details pages.

6.  Set any parameters and values for the task. Click **Add parameter** for each parameter-value pair you add.

    If you chose the `service` task, then you have two required parameters. For **action**, you can choose `restart`. For **service**, enter nginx.

    **Note:** Parameters marked with an asterisk \(`*`\) are required. Optional parameters are available in a dropdown. If there are no required or optional parameters, you can add arbitrary parameters provided those parameters are allowed in the task.

    Express values as strings, arrays, objects, integers, or booleans \(true or false\). You must express empty strings as two double quotes with no space \(`""`\). Wrap boolean values in double quotes \(for example, `"false"`\).

7.  Under **Schedule**, select **Later** and choose a start date, time, time zone, and frequency for the job to run.

    **Tip:** Based on the configuration of your console, the default time zone is either UTC, Coordinated Universal Time, or the local time, UTC offset, of your browser.

8.  From the list of target types, select **Node list**.

9.  Create the node list.

    1.  Expand the **WinRM nodes** target.

        **Note:** This target is available only for tasks permitted to run on all nodes.

    2.  Enter the target host names and the credentials required to access them.

    3.  Select additional target options.

        For example, to add a target port number, select **Target Port** from the drop-down list, enter the number, and click **Add**.

    4.  Click **Add nodes**. They are added to a table below.

    5.  Repeat these steps to add other nodes. You can add nodes with different credentials to create the node list.

        **Tip:** To remove a node from the table, select the checkbox next to it and click **Remove selected**.

10. After you have selected all your target nodes, click **Run job** or **Schedule job**.

    Your task run appears on the **Tasks** page. Click the **Run \#** to view the results and output.


## Run a task on a PQL query

Create a PQL query to run tasks on nodes that meet specific conditions.

Install the tasks you want to use.

Make sure you have access to the nodes you want to target.

Make sure you have permissions to run tasks and PQL queries.

1.  In the console, in the **Orchestration** section, click **Tasks**.

2.  Click **Run a task** in the upper right corner of the **Tasks** page.

3.  In the **Code environment** field, select the environment where you installed the module containing the task you want to run. This defaults to `production`.

4.  In the **Task** field, select a task to run, for example `service`.

    **Note:** If the tasks you expect are not available, you either have no tasks installed, or you don't have the correct permissions to run them.

5.  In the **Job description** field, provide a description. The text you enter here appears on the job list and job details pages.

6.  Set any parameters and values for the task. Click **Add parameter** for each parameter-value pair you add.

    If you chose the `service` task, then you have two required parameters. For **action**, you can choose `restart`. For **service**, enter nginx.

    **Note:** Parameters marked with an asterisk \(`*`\) are required. Optional parameters are available in a dropdown. If there are no required or optional parameters, you can add arbitrary parameters provided those parameters are allowed in the task.

    Express values as strings, arrays, objects, integers, or booleans \(true or false\). You must express empty strings as two double quotes with no space \(`""`\). Wrap boolean values in double quotes \(for example, `"false"`\).

7.  Under **Schedule**, select **Later** and choose a start date, time, time zone, and frequency for the job to run.

    **Tip:** Based on the configuration of your console, the default time zone is either UTC, Coordinated Universal Time, or the local time, UTC offset, of your browser.

8.  From the list of target types, select **PQL query**.

9.  Specify a target by doing one of the following:

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

10. Click **Submit query** and click **Refresh** to update the node results.

11. If you change or edit the query after it runs, click **Submit query** again.

12. To convert the PQL query target results to a node list, for use as a node list target, click **Convert query to static node list**.

    **Note:** If you select this option, the job target becomes a node list. You can add or remove nodes from the node list before running the job, but you cannot edit the query.

13. After you have selected all your target nodes, click **Run job** or **Schedule job**.

    Your task run appears on the **Tasks** page. Click the **Run \#** to view the results and output.


**Important:** When you run this job, the PQL query runs again, and the job might run on a different set of nodes than what is currently displayed. If you want the job to run only on the list as displayed, convert the query to a static node list before you run the job.

### Add custom PQL queries to the console

Add your own PQL queries to the console and quickly access them when running jobs.

1.  On your Master, as root, copy the `custom_pql_queries.json.example` file and remove the `example` suffix.

    ```
    cp
    /etc/puppetlabs/console-services/custom_pql_queries.json.example 
    /etc/puppetlabs/console-services/custom_pql_queries.json
    ```

2.  Edit the file contents to include your own PQL queries or remove any existing queries.

3.  Refresh the console UI in your browser.


You can now see your custom queries in the PQL drop down options when running jobs.

## Run a task on a node group

Similar to running a task on a list of nodes that you create in the console, you can run a task on a node group.

Install the tasks you want to use.

Make sure you have permission to run the tasks.

Make sure you have access to the nodes you want to target.

**Note:** If you do not have permissions to view a node group, or the node group doesn't have any matching nodes, that node group won't be listed as an option for viewing. In addition, a node group does not appear if no rules have been specified for it.

1.  In the console, in the **Orchestration** section, click **Tasks**.

2.  Click **Run a task** in the upper right corner of the **Tasks** page.

3.  In the **Code environment** field, select the environment where you installed the module containing the task you want to run. This defaults to `production`.

4.  In the **Task** field, select a task to run, for example `service`.

    **Note:** If the tasks you expect are not available, you either have no tasks installed, or you don't have the correct permissions to run them.

5.  In the **Job description** field, provide a description. The text you enter here appears on the job list and job details pages.

6.  Set any parameters and values for the task. Click **Add parameter** for each parameter-value pair you add.

    If you chose the `service` task, then you have two required parameters. For **action**, you can choose `restart`. For **service**, enter nginx.

    **Note:** Parameters marked with an asterisk \(`*`\) are required. Optional parameters are available in a dropdown. If there are no required or optional parameters, you can add arbitrary parameters provided those parameters are allowed in the task.

    Express values as strings, arrays, objects, integers, or booleans \(true or false\). You must express empty strings as two double quotes with no space \(`""`\). Wrap boolean values in double quotes \(for example, `"false"`\).

7.  Under **Schedule**, select **Later** and choose a start date, time, time zone, and frequency for the job to run.

    **Tip:** Based on the configuration of your console, the default time zone is either UTC, Coordinated Universal Time, or the local time, UTC offset, of your browser.

8.  From the list of target types, select **Node group**.

9.  In the **Choose a node group** box, type or select a node group, and click **Select**.

10. After you have selected all your target nodes, click **Run job** or **Schedule job**.

    Your task run appears on the **Tasks** page. Click the **Run \#** to view the results and output.


## Schedule a task

Schedule a job to run a task at a particular date and time or on a regular schedule.

Install the tasks you want to use.

Make sure you have access to the nodes you want to target.

1.  In the console, in the **Orchestration** section, click **Tasks**.

2.  In the **Job description** field, provide a description. The text you enter here appears on the job list and job details pages.

3.  In the **Task** field, select a task to run, for example `service`.

    **Note:** If the tasks you expect are not available, you either have no tasks installed, or you don't have the correct permissions to run them.

4.  Set any parameters and values for the task. Click **Add parameter** for each parameter-value pair you add.

    If you chose the `service` task, then you have two required parameters. For **action**, you can choose `restart`. For **service**, enter nginx.

    **Note:** Parameters marked with an asterisk \(`*`\) are required. Optional parameters are available in a dropdown. If there are no required or optional parameters, you can add arbitrary parameters provided those parameters are allowed in the task.

    Express values as strings, arrays, objects, integers, or booleans \(true or false\). You must express empty strings as two double quotes with no space \(`""`\). Wrap boolean values in double quotes \(for example, `"false"`\).

5.  Under **Schedule**, select **Later** and choose a start date, time, time zone, and frequency for the job to run.

    **Tip:** Based on the configuration of your console, the default time zone is either UTC, Coordinated Universal Time, or the local time, UTC offset, of your browser.

6.  To repeat the job on a regular schedule, change the run frequency from **Once** to **Hourly**, **Daily**, or **Weekly**.

    **Note:** If a recurring job runs longer than the selected frequency, the following job waits to start until the next available time interval.

7.  From the list of target types, select the category of nodes you want to target.

    -   **Node list** Add individual nodes by name.
    -   **PQL Query** Use the Puppet query language to retrieve a list of nodes.
    -   **Node group** Select an existing node group.
8.  Click **Schedule job**.

    Your task appears on the **Scheduled task** tab of the **Jobs** page.


**Related information**  


[Console and console-services parameters](config_console.md#)

[Delete a scheduled job](running_tasks_in_the_console.md#)

## Stop a job in progress

You can stop a job if, for example, you realize you need to reconfigure a class or push a configuration change that the job needs.

When you stop an on-demand Puppet run, runs that are underway finish, and runs that have not started are canceled.

1.  To stop a job:

    -   In the console, go to the **Jobs** page and select the **Puppet run** tab. From the list of jobs, find the one you want and click **Stop**.
    -   On the command line, press **CTRL + C**.

## Delete a scheduled job

Delete a job that is scheduled to run at a later time.

**Tip:** You must have the appropriate role-based permissions to delete another user's scheduled job.

1.  In the console, open the **Jobs** page.

2.  Click the **Scheduled Puppet Run** tab.

3.  From the list of jobs, find the one you want to delete and click **Remove**.


