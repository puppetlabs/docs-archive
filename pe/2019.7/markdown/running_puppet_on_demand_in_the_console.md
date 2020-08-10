---
author: Isaac Eldridge <isaac.eldridge@puppet.com\>
---

# Running Puppet on demand from the console

When you set up a job to run Puppet from the console, the orchestrator creates a job ID to track the job, shows you all nodes included in the job, and runs Puppet on those nodes in the appropriate order. Puppet compiles a new catalog for each node included in the job.

There are two ways to specify the job target \(the nodes you want to run jobs on\):

-   A static node list

-   A Puppet Query Language \(PQL\) query

-   A node group


You can't combine these methods, and if you switch from one to the other with the **Inventory** drop-down list, the target list clears and starts over. In other words, if you create a target list by using a node list, switching to a PQL query clears the node list, and vice versa. You can do a one-time conversion of a PQL query to a static node list if you want to add or remove nodes from the query results.

Before you start, be sure you have the correct permissions for running jobs. To run jobs on PQL queries, you need the "View node data from PuppetDB" permission.

## Run Puppet on a node list

Create a node list target for a job when you need to run Puppet on a specific set of nodes that isn't easily defined by a PQL query.

Make sure you have access to the nodes you want to target.

Make sure you have permissions to run jobs and PQL queries.

**Tip:** You can add network devices to a node list when you have installed modules for device transports in your production environment. You can find such modules in [Puppet Forge](https://forge.puppet.com/).

1.  In the console, on the **Jobs** page, click **Run Puppet**. 

2.  In the **Job description** field, provide a description. The text you enter here appears on the job list and job details pages.

3.  Under **Schedule**, select **Later** and choose a start date, time, time zone, and frequency for the job to run.

    **Tip:** Based on the configuration of your console, the default time zone is either UTC, Coordinated Universal Time, or the local time, UTC offset, of your browser.

4.  Select an environment:

    -   **Run nodes in their own assigned environment**: Nodes run in the environment specified by the Node Manager or their `puppet.conf` file.
    -   **Select an environment for nodes to run in**: Select an environment from the list. Nodes can run in an environment if their environment is agent-specified, if they're included in an application in that environment, or if they're classified in that environment by the node manager.
    **Note:** If your job target includes application instances, the selected environment also determines the dependency order of your node runs.

5.  Select the run mode for the job. The default run mode for a job always attempts to enforce new catalogs on nodes. To change the run mode, use the following selections:

    -   **No-op**: Simulate a Puppet run on all nodes in the job without enforcing a new catalog.
    -   **Debug**: Print all debugging messages.
    -   **Trace**: Print stack traces on some errors.
    -   **Eval-trace**: Display how long it took for each step to run.
    -   **Override noop = true configuration**: If any nodes in the job have `noop = true` set in their `puppet.conf` files, Puppet ignores that setting and enforce a new catalog on those nodes. This setting corresponds to the `--no-noop` flag available on the orchestrator CLI.
6.  In the **Inventory** list, select **Node list**.

7.  To create a node list, in the search field, start typing in the names of nodes to search for, and click **Search**.

    **Note:** The search does not handle regular expressions.

8.  Select the nodes you want to add to the job. You can select nodes from multiple searches to create the node list target.

9.  Click **Run job**.

    View the job status and a list of previous Puppet jobs on the **Jobs** page.


## Run Puppet on a PQL query

For some jobs, you might want to target nodes that meet specific conditions. For such jobs, create a PQL query.

Make sure you have access to the nodes you want to target.

Make sure you have permissions to run jobs and PQL queries.

1.  In the console, on the **Jobs** page, click **Run Puppet**. 

2.  In the **Job description** field, provide a description. The text you enter here appears on the job list and job details pages.

3.  Under **Schedule**, select **Later** and choose a start date, time, time zone, and frequency for the job to run.

    **Tip:** Based on the configuration of your console, the default time zone is either UTC, Coordinated Universal Time, or the local time, UTC offset, of your browser.

4.  Select an environment:

    -   **Run nodes in their own assigned environment**: Nodes run in the environment specified by the Node Manager or their `puppet.conf` file.
    -   **Select an environment for nodes to run in**: Select an environment from the list. Nodes can run in an environment if their environment is agent-specified, if they're included in an application in that environment, or if they're classified in that environment by the node manager.
    **Note:** If your job target includes application instances, the selected environment also determines the dependency order of your node runs.

5.  Select the run mode for the job. The default run mode for a job always attempts to enforce new catalogs on nodes. To change the run mode, use the following selections:

    -   **No-op**: Simulate a Puppet run on all nodes in the job without enforcing a new catalog.
    -   **Debug**: Print all debugging messages.
    -   **Trace**: Print stack traces on some errors.
    -   **Eval-trace**: Display how long it took for each step to run.
    -   **Override noop = true configuration**: If any nodes in the job have `noop = true` set in their `puppet.conf` files, Puppet ignores that setting and enforce a new catalog on those nodes. This setting corresponds to the `--no-noop` flag available on the orchestrator CLI.
6.  From the list of target types, select **PQL query**.

7.  Specify a target by doing one of the following:

    -   Enter a query that selects the target you want. See the [Puppet Query Language \(PQL\) reference](https://puppet.com/docs/puppetdb/latest/api/query/v4/pql.html) for more information.
    -   Click **Common queries**. Select one of the queries and replace the defaults in the braces \(`{ }`\) with values that specify the target you want.

        **Note:** These queries include `[certname]` as `[<projection>]` to restrict the output.

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

11. Click **Run job**.

    View the job status and a list of previous Puppet jobs on the **Jobs** page.


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

## Run Puppet on a node group

Create a node target for a job when you need to run Puppet on a specific set of nodes in a pre-defined group.

Make sure you have access to the nodes you want to target.

**Note:** If you do not have permissions to view a node group, or the node group doesn't have any matching nodes, that node group won't be listed as an option for viewing. In addition, a node group does not appear if no rules have been specified for it.

1.  In the console, on the **Jobs** page, click **Run Puppet**. 

2.  In the **Job description** field, provide a description. The text you enter here appears on the job list and job details pages.

3.  Under **Schedule**, select **Later** and choose a start date, time, time zone, and frequency for the job to run.

    **Tip:** Based on the configuration of your console, the default time zone is either UTC, Coordinated Universal Time, or the local time, UTC offset, of your browser.

4.  Select an environment:

    -   **Run nodes in their own assigned environment**: Nodes run in the environment specified by the Node Manager or their `puppet.conf` file.
    -   **Select an environment for nodes to run in**: Select an environment from the list. Nodes can run in an environment if their environment is agent-specified, if they're included in an application in that environment, or if they're classified in that environment by the node manager.
    **Note:** If your job target includes application instances, the selected environment also determines the dependency order of your node runs.

5.  Select the run mode for the job. The default run mode for a job always attempts to enforce new catalogs on nodes. To change the run mode, use the following selections:

    -   **No-op**: Simulate a Puppet run on all nodes in the job without enforcing a new catalog.
    -   **Debug**: Print all debugging messages.
    -   **Trace**: Print stack traces on some errors.
    -   **Eval-trace**: Display how long it took for each step to run.
    -   **Override noop = true configuration**: If any nodes in the job have `noop = true` set in their `puppet.conf` files, Puppet ignores that setting and enforce a new catalog on those nodes. This setting corresponds to the `--no-noop` flag available on the orchestrator CLI.
6.  From the list of target types, select **Node group**.

7.  In the **Choose a node group** box, type or select a node group, and click **Select**.

8.  Click **Run job**.

    View the job status and a list of previous Puppet jobs on the **Jobs** page.


## Run jobs throughout the console

You don't need to be in the Jobs section of the console to run a Puppet job on your nodes. You might encounter situations where you want to run jobs on lists of nodes derived from different pages in the console.

You can create jobs from the following pages:

|Page|Description|
|----|-----------|
|**Overview**|This page shows a list of all your managed nodes, and gathers essential information about your infrastructure at a glance.|
|**Events**|Events let you view a summary of activity in your infrastructure, analyze the details of important changes, and investigate common causes behind related events. For instance, let's say you notice run failures because some nodes have out-of-date code. After you update the code, you can create a job target from the list of failed nodes to be sure you're directing the right fix to the right nodes. You can create new jobs from the Nodes with events category.|
|**Classification node groups**|Node groups are used to automate classification of nodes with similar functions in your infrastructure. If you make a classification change to a node group, you can quickly create a job to run Puppet on all the nodes in that group, pushing the change to all nodes at one time.|

Make sure you have permissions to run jobs and PQL queries.

1.  In the console, in the **Run** section, click **Puppet**.

    At this point, the list of nodes is converted to a new Puppet run job list target.

2.  In the **Job description** field, provide a description. The text you enter here appears on the job list and job details pages.

3.  Select an environment:

    -   **Run nodes in their own assigned environment**: Nodes run in the environment specified by the Node Manager or their `puppet.conf` file.
    -   **Select an environment for nodes to run in**: Select an environment from the list. Nodes can run in an environment if their environment is agent-specified, if they're included in an application in that environment, or if they're classified in that environment by the node manager.
    **Note:** If your job target includes application instances, the selected environment also determines the dependency order of your node runs.

4.  Select the run mode for the job. The default run mode for a job always attempts to enforce new catalogs on nodes. To change the run mode, use the following selections:

    -   **No-op**: Simulate a Puppet run on all nodes in the job without enforcing a new catalog.
    -   **Debug**: Print all debugging messages.
    -   **Trace**: Print stack traces on some errors.
    -   **Eval-trace**: Display how long it took for each step to run.
    -   **Override noop = true configuration**: If any nodes in the job have `noop = true` set in their `puppet.conf` files, Puppet ignores that setting and enforce a new catalog on those nodes. This setting corresponds to the `--no-noop` flag available on the orchestrator CLI.
5.  **Important:** Do not change the Inventory from **Node list** to **PQL query**. This clears the node list target.

6.  Click **Run job**.

    View the job status and a list of previous Puppet jobs on the **Jobs** page.


## Schedule a Puppet run

Schedule a job to deploy configuration changes at a particular date and time.

Make sure you have access to the nodes you want to target.

1.  In the console, on the **Jobs** page, click **Run Puppet**. 

2.  In the **Job description** field, provide a description. The text you enter here appears on the job list and job details pages.

3.  Select an environment:

    -   **Run nodes in their own assigned environment**: Nodes run in the environment specified by the Node Manager or their `puppet.conf` file.
    -   **Select an environment for nodes to run in**: Select an environment from the list. Nodes can run in an environment if their environment is agent-specified, if they're included in an application in that environment, or if they're classified in that environment by the node manager.
    **Note:** If your job target includes application instances, the selected environment also determines the dependency order of your node runs.

4.  Under **Schedule**, select **Later** and choose a start date, time, time zone, and frequency for the job to run.

    **Tip:** Based on the configuration of your console, the default time zone is either UTC, Coordinated Universal Time, or the local time, UTC offset, of your browser.

5.  To repeat the job on a regular schedule, change the run frequency from **Once** to **Hourly**, **Daily**, or **Weekly**.

    **Note:** If a recurring job runs longer than the selected frequency, the following job waits to start until the next available time interval.

6.  Select the run mode for the job. The default run mode for a job always attempts to enforce new catalogs on nodes. To change the run mode, use the following selections:

    -   **No-op**: Simulate a Puppet run on all nodes in the job without enforcing a new catalog.
    -   **Debug**: Print all debugging messages.
    -   **Trace**: Print stack traces on some errors.
    -   **Eval-trace**: Display how long it took for each step to run.
    -   **Override noop = true configuration**: If any nodes in the job have `noop = true` set in their `puppet.conf` files, Puppet ignores that setting and enforce a new catalog on those nodes. This setting corresponds to the `--no-noop` flag available on the orchestrator CLI.
7.  From the list of target types, select the category of nodes you want to target.

    -   **Node list** Add individual nodes by name.
    -   **PQL Query** Use the Puppet query language to retrieve a list of nodes.
    -   **Node group** Select an existing node group.
8.  Click **Schedule job**.

    Your job appears on the **Scheduled Puppet run** tab of the **Jobs** page.


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


