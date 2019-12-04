# Reviewing jobs in the console

In the console, you can view a list of recent jobs and drill down to see useful details about each one.

## Job list page

The Job list page provides a quick glance at key information about jobs run in the console, either as tasks, on-demand Puppet runs, or single-node runs. It also gathers information for jobs run on the orchestrator command-line interface.

On this page, jobs have one of the following statuses:

-   In progress
-   Succeeded
-   Finished with failures
-   Stopping
-   Stopped

Jobs are sorted chronologically by timestamp, and the timestamp reflects the job's most recent status change \(when the job started, stopped, or completed\). Filter by job type to see only Puppet runs or tasks, or to see both.

There's also a link to the profile of user who ran the job. If a user is deleted, the UUID for that user will show instead of their name.

To examine a job's details, click its **Job ID**. Note that the **Job ID** is only a link if you have the correct [RBAC permissions](rbac_permissions_intro.md#) to trigger a Puppet run.

## Job details page

The Job details page provides a variety of information about jobs run from the console or the orchestrator command line interface.

This page captures the job's start time duration, and provides a link to the user who ran the job. The **Job details** link shows the job's inventory target, environment, concurrency \(if set on a job run from the CLI\), and run mode. For task jobs, the **Job details** link show the job's inventory target and any parameters and values set for the task.

There's also a link to the profile of user who ran the job. If a user is deleted, the UUID for that user will show instead of their name.

On this page, jobs have one of the following statuses:

-   Succeeded
-   Failed
-   Skipped

### Reviewing the node run results table

In the Node run results table, node runs in the job are sorted by status and then by certname. The table shows the number of total resources affected on each node in the job, and also tracks the following event types for each node:

|Event type|Description|
|----------|-----------|
|Failure|A property was out of sync. Puppet tried to make changes, but was unsuccessful. To learn more about a failed node, click the node link and use the Reports page to drill down through its events.|
|Corrective change|Puppet found an inconsistency between the last applied catalog and a property’s configuration and corrected the property to match the catalog.|
|Intentional change|Puppet applied catalog changes to a property.|
|Skipped resource|A prerequisite for this resource was not met. This prerequisite is either one of the resource’s dependencies or a timing limitation set with the schedule metaparameter. The resource might be in sync or out of sync; Puppet doesn’t know yet.|

Additionally, you can use the **Job node status** filter to filter nodes in this table based on their status. For example, if a job has completed and there were failures, you can filter to view only failed nodes, so you know which nodes to investigate further.

Nodes can have the following statuses:

-   Succeeded
-   Failed
-   Skipped
-   In progress \(Only used for running jobs.\)
-   Queued

To view a node's run report, click the link in its **Report** column.

To export job details to a CSV file, click **Export data**. The CSV includes the same information as the Node run results table.

### Reviewing the node run results table for tasks

The node run results returned by the orchestrator will vary depending on the task type. If output is available for a task that completes successfully, it will include the number of nodes affected and any parameters you specified in the task. Output is shown in standard output \(stdout\) or structured output. For example, a exec task that runs the `ls` command on your nodes will show results in stdout output, but a package upgrade task will most likely show results in structured output.

Some tasks can successfully complete but have no output. In such cases, the orchestrator returns a `No output for this node` message.

When tasks fail, orchestrator will return a structured error that describes the failure.

### Rerunning jobs from the details page

On the **job details** page, you can run a job again, using the same settings specified in the original job. You must have the correct permissions to run Puppet or tasks to rerun a job.

To run a job again, click the **Run again** button. This action sets up a new job using the same settings from the original job. The new job recreates the PQL query or node list target and runs on the same nodes.

Jobs with application targets run from the orchestrator command line cannot be rerun from the console, as the console doesn't support these targets.

Additionally, the `whole_environment` target is deprecated, so jobs with that target cannot be rerun from the console or command line interface.

