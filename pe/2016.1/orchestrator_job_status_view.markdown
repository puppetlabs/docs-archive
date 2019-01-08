---
layout: default
title: "Reviewing Puppet jobs"
canonical: "/pe/latest/orchestrator_job_status_view.html"
---

Use the `puppet job show` command to view running or completed orchestration jobs.

>**Note:** The `puppet job show` command replaces `puppet job list`. The `puppet job list` command has been aliased to `puppet job show`.

## View a running or completed job

To check the status of a running or completed job, use the following command:

~~~
puppet job show <job ID>
~~~

The command returns the following:

- The status of the job (running, completed, or failed).
- The start (and finish) time.
- The elapsed time or duration of the run.
- The user who ran the job.
- The environment the job ran in.

### Node run status

In addition to details about the job, the command returns a list of nodes in the run. Node runs can be **in progress**, **completed**, **skipped**, or **failed**.

- For a **completed** node run, the orchestrator prints the following information:
   - The configuration version.
   - The transaction ID.
   - A summary of resource events.
   - A link to the full node run report in the PE console.

- For an **in progress** node run, the orchestrator prints how many seconds ago the run started.

- For a **failed** node run, the orchestrator prints an error message indicating why the run failed. In this case, any additional runs are **skipped**. 

   When a run fails, the orchestrator also prints what applications were affected by the failure, as well as any applications that were affected by skipped node runs.

## View a list of jobs

To view a list of running and completed jobs, up to 50 maximum (the concurrency limit), ordered by timestamp, use the following command:

~~~
puppet job show <OPTIONS>
~~~

### Command options

The following are common options you may use with the `show` action. For a complete list of global options run `puppet job --help`.

Option | Values | Description
-----------|-----------|-----------
`--service-url`  | flag | Overrides the URL setting for the orchestrator service.
- `--environment`, `--env`, `-e`| environment name | Overrides the environment specified in your orchestrator configuration file.

Run the command without any options to return a list of all jobs in all environments (up to 50 jobs maximum).

The command returns something like the following:

~~~
ID  STATUS    TIMESTAMP              USER            ENVIRONMENT  TARGET
----------------------------------------------------------------------------------
234 Running   14:44:03 05-13-15 PST  <Job run user>  production   all
235 Complete  12:12:56 05-12-15 PST  <Job run user>  dev          ExampleApp::FullAppStack
236 Complete  03:33:03 05-03-15 PST  <Job run user>  production   ExampleApp['example\_app_instance']
~~~
