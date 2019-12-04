---
layout: default
title: "Reviewing jobs in the console"
canonical: "/pe/latest/orchestrator_review_console.html"
---

In the console, you can view a list of recent jobs and drill down to see useful details about each one.

## Job list page

The job list page provides a quick glance at key information about jobs run from the console, either from the Jobs page or by clicking **Run Puppet** for a node, or from the orchestrator command-line interface. 

On this page, jobs have one of the following statuses:

- **In progress**
- **Succeeded**
- **Finished with failures**
- **Stopping**
- **Stopped**

Jobs are sorted chronologically by timestamp, and the timestamp reflects the job's most recent status change (when the job started, stopped, or completed).

There's also a link to the profile of user who ran the job. If a user is deleted, the UUID for that user will show instead of their name.

To examine a job's details, click its Job ID.

## Job details page

The job details page provides a variety of information about a job.  

This page captures the job's start time duration, and provides a link to the user who ran the job. The **Job details** link shows the job's inventory target and environment.

There's also a link to the profile of user who ran the job. If a user is deleted, the UUID for that user will show instead of their name.

On this page, jobs have one of the following statuses:

- **Succeeded**
- **Failed**
- **Skipped**

Node runs in the job are sorted by status and then by certname. The node run results table shows the number of total resources affected on each node in the job, and also tracks the following event types for each node:

Event type | Description
-----------| -----------
Failure | A property was out of sync. Puppet tried to make changes, but was unsuccessful.
Corrective change |	Puppet found an inconsistency between the last applied catalog and a property’s configuration and corrected the property to match the catalog.
Intentional change | Puppet applied catalog changes to a property.
Skipped resource | A prerequisite for this resource was not met. This prerequisite is either one of the resource’s dependencies or a timing limitation set with the schedule metaparameter. The resource might be in sync or out of sync; Puppet doesn’t know yet.

To view a node's run report, click the link in its **Report** column.

To learn more about a failed node, click the node link and use the **Reports** page to drill down through its events.


**********

**Related links:**

- [Stop a running job](./orchestrator_job_run_console.html#stop-a-job).

- [Reviewing node run reports](./CM_reports.html#working-with-the-reports-page).