---
author: Isaac Eldridge <isaac.eldridge@puppet.com\>
---

# Running Puppet on demand in the console

When you set up a job to run Puppet from the console, the orchestrator creates a job ID to track the job, shows you all nodes included in the job, and runs Puppet on those nodes in the appropriate order. Puppet compiles a new catalog for each node included in the job.

There are three ways to specify the job target \(the nodes you want to run jobs on\):

-   A Puppet Query Language \(PQL\) query

-   A static node list

-   A node group


You can't combine these methods, and if you switch from one to the other with the **Inventory** drop-down list, the target list clears and starts over. In other words, if you create a target list by using a node list, switching to a PQL query clears the node list, and vice versa. You can do a one-time conversion of a PQL query to a static node list if you want to add or remove nodes from the query results.

Before you start, be sure you have the correct permissions for running jobs. To run jobs on PQL queries, you need the "View node data from PuppetDB" permission.

## Run Puppet on a PQL query

For some jobs, you may want to target nodes that meet specific conditions. For such jobs, create a PQL query.

### About this task

Make sure you have permissions to run jobs and PQL queries.

### Procedure

1.  In the console, in the Run section, click **Puppet**. 

2.  In the **Job description** field, provide a description that will be shown on the job list and job details pages.

3.  Select an environment:

    -   **Run nodes in their own assigned environment**: Nodes will run in the environment specified by the Node Manager or their `puppet.conf` file.
    -   **Select an environment for nodes to run in**: Select an environment from the list. Nodes can run in an environment if their environment is agent-specified, if they're included in an application in that environment, or if they're classified in that environment by the node manager.
    **Note:** If your job target includes application instances, the selected environment will also determine the dependency order of your node runs.

4.  Select the run mode for the job. The default run mode for a job always attempts to enforce new catalogs on nodes. To change the run mode, use the following selections:

    -   **No-op**: Simulate a Puppet run on all nodes in the job without enforcing a new catalog.
    -   **Override noop = true configuration**: If any nodes in the job have `noop = true` set in their `puppet.conf` files, Puppet will ignore that setting and enforce a new catalog on those nodes. This setting corresponds to the `--no-noop` flag available on the orchestrator CLI.
5.  In the **Inventory** list, select **PQL query**.

6.  Specify a target by doing one of the following:

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

7.  Click **Submit query** and click **Refresh** to update the node results.

8.  If you change or edit the query after it runs, click **Submit query** again.

9.  To convert the PQL query target results to a node list, for use as a node list target, click **Convert query to static node list**.

    **Note:** If you select this option, the job target becomes a node list. You can add or remove nodes from the node list before running the job, but you cannot edit the query.

10. Click **Run job**.


### Results

**Important:** When you run this job, the PQL query will run again, and the job may run on a different set of nodes than what is currently displayed. If you want the job to run only on the list as displayed, convert the query to a static node list before you run the job.

## Run Puppet on a node list

Create a node list target for a job when you need to run Puppet on a specific set of nodes that isn't easily defined by a PQL query.

### About this task

Make sure you have permissions to run jobs and PQL queries.

### Procedure

1.  In the console, in the Run section, click **Puppet**. 

2.  In the **Job description** field, provide a description that will be shown on the job list and job details pages.

3.  Select an environment:

    -   **Run nodes in their own assigned environment**: Nodes will run in the environment specified by the Node Manager or their `puppet.conf` file.
    -   **Select an environment for nodes to run in**: Select an environment from the list. Nodes can run in an environment if their environment is agent-specified, if they're included in an application in that environment, or if they're classified in that environment by the node manager.
    **Note:** If your job target includes application instances, the selected environment will also determine the dependency order of your node runs.

4.  Select the run mode for the job. The default run mode for a job always attempts to enforce new catalogs on nodes. To change the run mode, use the following selections:

    -   **No-op**: Simulate a Puppet run on all nodes in the job without enforcing a new catalog.
    -   **Override noop = true configuration**: If any nodes in the job have `noop = true` set in their `puppet.conf` files, Puppet will ignore that setting and enforce a new catalog on those nodes. This setting corresponds to the `--no-noop` flag available on the orchestrator CLI.
5.  In the **Inventory** list, select **Node list**.

6.  To create a node list, in the search field, start typing in the names of nodes to search for, and click **Search**.

    **Note:** The search does not handle regular expressions.

7.  Select the nodes you want to add to the job. You can select nodes from multiple searches to create the node list target.

8.  To remove any nodes from the target, select them and click **Remove selected**, or first click **Select all** and then click **Remove selected**.

9.  Click **Run job**.


### Results

**Important:** When you run this job, the PQL query will run again, and the job may run on a different set of nodes than what is currently displayed. If you want the job to run only on the list as displayed, convert the query to a static node list before you run the job.

## Run Puppet on a node group

Create a node target for a job when you need to run Puppet on a specific set of nodes in a pre-defined group.

### About this task

**Note:** If you do not have permissions to view a node group, or the node group doesn't have any matching nodes, that node group won't be listed as an option for viewing. In addition, a node group will not appear if no rules have been specified for it.

### Procedure

1.  In the console, in the Run section, click **Puppet**. 

2.  In the **Job description** field, provide a description that will be shown on the job list and job details pages.

3.  Select an environment:

    -   **Run nodes in their own assigned environment**: Nodes will run in the environment specified by the Node Manager or their `puppet.conf` file.
    -   **Select an environment for nodes to run in**: Select an environment from the list. Nodes can run in an environment if their environment is agent-specified, if they're included in an application in that environment, or if they're classified in that environment by the node manager.
    **Note:** If your job target includes application instances, the selected environment will also determine the dependency order of your node runs.

4.  Select the run mode for the job. The default run mode for a job always attempts to enforce new catalogs on nodes. To change the run mode, use the following selections:

    -   **No-op**: Simulate a Puppet run on all nodes in the job without enforcing a new catalog.
    -   **Override noop = true configuration**: If any nodes in the job have `noop = true` set in their `puppet.conf` files, Puppet will ignore that setting and enforce a new catalog on those nodes. This setting corresponds to the `--no-noop` flag available on the orchestrator CLI.
5.  In the **Inventory** list, select **Node group**.

6.  In the **Choose a node group** box, type or select a node group, and click **Select**.

7.  Click **Run job**.


### Results

## Run jobs throughout the console

You don't need to be in the Jobs section of the console to run a Puppet job on your nodes. It's likely that you'll encounter situations where you want to run jobs on lists of nodes derived from different pages in the console.

### About this task

You can create jobs from the following pages:

|Page|Description|
|----|-----------|
|**Overview**|This page shows a list of all your managed nodes, and gathers essential information about your infrastructure at a glance.|
|**Events**|Events let you view a summary of activity in your infrastructure, analyze the details of important changes, and investigate common causes behind related events. For instance, let's say you notice run failures because some nodes have out-of-date code. Once you update the code, you can create a job target from the list of failed nodes to be sure you're directing the right fix to the right nodes. You can create new jobs from the Nodes with events category.|
|**Classification node groups**|Node groups are used to automate classification of nodes with similar functions in your infrastructure. If you make a classification change to a node group, you can quickly create a job to run Puppet on all the nodes in that group, pushing the change to all nodes at once.|

Make sure you have permissions to run jobs and PQL queries.

### Procedure

1.  In the console, in the **Run** section, click **Puppet**.

    At this point, the list of nodes is converted to a new Puppet run job list target.

2.  In the **Job description** field, provide a description that will be shown on the job list and job details pages.

3.  Select an environment:

    -   **Run nodes in their own assigned environment**: Nodes will run in the environment specified by the Node Manager or their `puppet.conf` file.
    -   **Select an environment for nodes to run in**: Select an environment from the list. Nodes can run in an environment if their environment is agent-specified, if they're included in an application in that environment, or if they're classified in that environment by the node manager.
    **Note:** If your job target includes application instances, the selected environment will also determine the dependency order of your node runs.

4.  Select the run mode for the job. The default run mode for a job always attempts to enforce new catalogs on nodes. To change the run mode, use the following selections:

    -   **No-op**: Simulate a Puppet run on all nodes in the job without enforcing a new catalog.
    -   **Override noop = true configuration**: If any nodes in the job have `noop = true` set in their `puppet.conf` files, Puppet will ignore that setting and enforce a new catalog on those nodes. This setting corresponds to the `--no-noop` flag available on the orchestrator CLI.
5.  **Important:** Do not change the Inventory from **Node list** to **PQL query**. This will clear the node list target.

6.  Click **Run job**.


### Results

## Stop a job in progress

You can stop a job if, for example, you realize you need to reconfigure a class or push a configuration change that the job needs.

### About this task

When you stop an on-demand Puppet run, runs that are underway will finish, and runs that have not started will not start.

Stopping tasks depends on how many nodes are targeted in the job versus the concurrency limit you've set. If the concurrency limit is higher than the number of nodes targeted, all nodes will complete the task, as those nodes will have already started the task.

### Procedure

1.  To stop a job:

    -   In the console, go to the **Job details** page and select the **Puppet run** or **Task** tab. From the list of jobs, find the one you want and click **Stop**.
    -   On the command line, press **CTRL + C**.

