---
layout: default
title: "Running orchestrator jobs from the console"
canonical: "/pe/latest/orchestrator_job_run_console.html"
---

Run jobs from the console to roll out changes on nodes in your infrastructure. For example, did you just add a new class parameter to a set of nodes? Build a PQL query to identify that list of nodes, and run a job to make that change on those nodes. Or did you just deploy Puppet code to a new environment? Use a job to run Puppet across a list of nodes in that environment.

When you run a job from the console, the orchestrator creates a job ID to track the job, shows you all nodes included in the job, and runs Puppet on those nodes in the appropriate order. Puppet compiles a new catalog for each node included in the job.

There are two ways to specify the job target (the nodes you want to run jobs on):

- A Puppet Query Language (PQL) query
- A static node list

You can't combine these methods, and if you switch from one to the other with the **Inventory** drop-down list, the target list clears and starts over. In other words, if you create a target list by using a node list, switching to a PQL query clears the node list, and vice versa. You can do a one-time conversion of a PQL query to a static node list if you want to add or remove nodes from the query results.

Before you start, be sure you have [the correct permissions for running jobs](./orchestrator_prep_console.html#setting-pe-rbac-permissions-for-orchestrator). To run jobs on PQL queries, you need the "View node data from PuppetDB" permission.

### Run a job on a PQL query

For some orchestrator jobs, you may want to target nodes that meet specific conditions. For such jobs, create a PQL query.

To run jobs on PQL queries, you need the "View node data from PuppetDB" permission.

Learn more about [writing PQL queries]({{puppetdb}}/api/query/tutorial-pql.html).

1. In the console, click **Jobs** > **New job**.
2. In the **Job description** field, provide a description that will be shown on the job list and job details pages.
3. Select an environment:

   - **Run nodes in their own assigned environment:** Nodes will run in the environment specified by the Node Manager or their `puppet.conf` file.

   - **Select an environment for nodes to run in:** Select an environment from the list. Nodes can run in an environment if their environment is agent-specified, if they're included in an application in that environment, or if they're classified in that environment by the Node Manager.

   >**Note:** If your job target includes Puppet application instances, the selected environment will also determine the dependency order of your node runs.

4. Select whether or not to run this job in no-op mode.

   Nodes configured for `noop = true` will always run in no-op mode.

5. In the **Inventory** list, select **PQL query**.
6. To specify a target:

   - Enter a query that selects the target you want.

   - Click **Common queries**. Select one of the queries and replace the defaults in the braces (`{ }`) with values that will specify the target you want.

      Target | PQL query
      -------|-----------
      All nodes | `nodes[certname] { }`
      Nodes with a specific resource (example: httpd) | `resources[certname] { type = "Service" and title = "httpd" }`
      Nodes with a specific fact and value (example: OS name is CentOS) | `inventory[certname]  { facts.os.name = "CentOS" }`
      Nodes with a specific report status (example: last run failed) | `reports[certname]  { latest_report_status = "failed" }`
      Nodes with a specific class (example: Apache) | `resources[certname]  { type = "Class" and title = "Apache" }`
      Nodes assigned to a specific environment (example: production) | `nodes[certname]  { catalog_environment = "production" }`
      Nodes with a specific version of a resource type (example: openSSL is v1.1.0e) | `resources[certname]  {type = "Package" and title="openssl" and parameters.ensure = "1.0.1e-51.el7_2.7" }`
      Nodes with a specific resource and operating system (example: httpd and CentOS) | `inventory[certname]  { facts.operatingsystem = "CentOS" and resources { type = "Service" and title = "httpd" } }`

7. Click **Submit query** and click **Refresh** to update the node results.

   If you change or edit the query after it runs, click **Submit query** again.

8. (Optional) To convert the PQL query target results to a node list, for use as a node list target, click **Convert query to static node list**.

   >**Note:** If you select this option, the job target becomes a node list. You can add or remove nodes from the node list before running the job, but you cannot edit the query.

9. Review the job plan.

10. Click **Run job**.

   >**Important:** When you run this job, the PQL query will run again, and the job may run on a different set of nodes than what is currently displayed. If you want the job to run only on the list as displayed, convert the query to a static node list before you run the job.

### Run a job on a node list

Create a node list target for an orchestrator job when you need to run a job on a specific set of nodes that isn't easily defined by a PQL query.

1. In the console, click **Jobs** > **New job**.
2. In the **Job description** field, provide a description that will be shown on the job list and job details pages.
3. Select an environment:

   - **Run nodes in their own assigned environment:** Nodes will run in the environment specified by the Node Manager or their `puppet.conf` file.

   - **Select an environment for nodes to run in:** Select an environment from the list. Nodes can run in an environment if their environment is agent-specified, if they're included in an application in that environment, or if they're classified in that environment by the Node Manager.

   >**Note:** If your job target includes Puppet application instances, the selected environment will also determine the dependency order of your node runs.

4. Select whether or not to run this job in no-op mode.

   Nodes configured for `noop = true` will always run in no-op mode.

5. In the **Inventory** list, select **Node list**.
6. In the search field, start typing in the names of nodes to search for, and click **Search**.

   >**Note:** The search does not handle regular expressions.

7. Select the nodes you want to add to the job.

   You can select nodes from multiple searches to create the node list target.

8. Review the job plan.

   To remove any nodes from the target, select them and click **Remove**, or click **Remove all**.

10. Click **Run job**.


## Stop a job

You can stop a job if, for example, you realize you need to reconfigure a class or push a configuration change that the job needs.

When you stop a job, Puppet runs that are underway will finish. Runs that have not started will not start.

1. Navigate to the **Job details** page for the job you want to stop, and click **Stop**.


*******

**Related links:**

- Use the [job details page](./orchestrator_review_console.html#job-details-page) to view real-time results for jobs run from the console or command line.

- You can also [run jobs from the command line](./orchestrator_job_run.html).


