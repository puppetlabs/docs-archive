# Review jobs from the console

View a list of recent jobs and drill down to see useful details about each one.

**Parent topic:**[Reviewing jobs](reviewing_jobs.md)

## Job list page

The Job list page provides a quick glance at key information about jobs run from the console. It also gathers information for jobs run from the orchestrator command-line interface.

Jobs are organized by type: **Puppet run**, **Task**, **Plan** \(a plan combines multiple tasks and runs them with a single Bolt command\) and **Scheduled**. And they are sorted by the date and time of the job's most recent status change; when the job started, stopped, completed or is scheduled to run.

Puppet run, Task, and Plan jobs have one of the following statuses:

-   In progress
-   Succeeded
-   Finished with failures
-   Stopping
-   Stopped

### View the Jobs list page

1.  From the console sidebar, click **Jobs**.

2.  To view the details of a job, click its **Job ID**.

    The **Job ID** is a link only if you have the correct permissions to run the related job.

3.  To view the profile of the user who ran the job, click the user name link.

    A UUID appears for user records that have been deleted.


**Related information**  


[User permissions](rbac_permissions_intro.md#)

[Using Bolt with orchestrator](bolt_configure_orchestrator.md)

## Job details page

The **Job** details page captures the job start time, duration, user who ran the job, and node run results status: Succeeded, Failed, or Skipped.

### View the Job details page

1.  Click the **Job ID** on the **Job** list page to view the **Job** details page.

2.  To view the profile of the user who ran the job, click the user name link.

    A UUID appears for user records that have been deleted.

3.  Click the **Job Details** link for more information.

-   Puppet run details include the node target type, environment, concurrency \(if set on a job run from the command line\), and run mode.

-   Task run details include the node target type and any parameters and values set for the task.

-   Plan run details include plan output and links to the invidiual task run details.

4.  Review the **Node run results** table for invidiual node run results.

    This table is available for Puppet run and task jobs. For nodes that have the Puppet agent installed, you can click a node name link to view the node detail page.


### Node run results table for Puppet run jobs

In the **Node run results** table, nodes are sorted by status and then by certname. Use the table to view the number of total resources affected on each node in the job and track the event types for each node.

|Event type|Description|
|----------|-----------|
|Corrective change|Puppet found an inconsistency between the last applied catalog and a property’s configuration and corrected the property to match the catalog.|
|Failure|A property was out of sync. Puppet tried to make changes, but was unsuccessful. To learn more about a failed node, click the node link and use the Reports page to drill down through its events.|
|Intentional change|Puppet applied catalog changes to a property.|
|Skipped resource|A prerequisite for this resource was not met. This prerequisite is either one of the resource’s dependencies or a timing limitation set with the schedule metaparameter. The resource might be in sync or out of sync; Puppet doesn’t know yet.|

Additionally, you can use the **Job node status** filter to filter nodes in this table based on their status. For example, if a job has completed and there were failures, you can filter to view only failed nodes, so you know which nodes to investigate further.

Nodes can have the following statuses:

-   Succeeded
-   Failed
-   Failed - error...

-   Skipped
-   In progress \(Used only for running jobs.\)
-   Queued

To view a node's run report, click the link in its **Report** column.

To export the node run results to a CSV file, click **Export data**. The CSV includes the same information as the table.

### Node run results table for task jobs

The node run results returned by the orchestrator vary depending on the task type. Output is shown in either standard output \(stdout\) or structured output. For example, a exec task that runs the `ls` command on your nodes shows results in stdout output, but a package upgrade task most likely shows results in structured output.

If output is available for a task that completes successfully, the results table includes the number of nodes affected and any parameters specified in the task. Some tasks can successfully complete but have no output. In such cases, the orchestrator returns a `No output for this node` message.

When tasks fail, orchestrator returns a structured error that describes the failure.

**Tip:** Links to the node details screen are available only for nodes that have the Puppet agent installed.

### Plan output and event log

You can find the output and result of plan job runs by navigating to the **Plan** tab in the **Job** page. Click on the **Job ID** for the plan you want to view to see the main job output, plus the event log broken down by step. Since plans include multiple steps, tasks, or scripts, you can view the output and result of each item separately.

### Rerunning jobs from the details page

On the Job details page, you can run a job again, using the same settings specified in the original job. You must have the correct permissions to run Puppet or tasks to rerun a job.

To run a job again, click the **Run again** button. This action sets up a new job using the same settings from the original job. The new job recreates the PQL query or node list target and runs on the same nodes.

Jobs with application targets run from the orchestrator command line cannot be rerun from the console, as the console doesn't support these targets. Similarly, tasks run as part of a plan cannot be run from the console \(a plan combines multiple tasks and runs them with a single Bolt command\). 

Additionally, the `whole_environment` target is deprecated, so jobs with that target cannot be rerun from the console or command line interface.

