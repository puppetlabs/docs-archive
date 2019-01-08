---
layout: default
title: "Running Puppet jobs"
canonical: "/pe/latest/orchestrator_job_run.html"
---

Three actions are available with the puppet-job command:

- Run a job.
- Check the plan of a potential job run, described in [Checking Puppet job plans](./orchestrator_job_plan.html).
- View the *details* of a running or completed job, or view a *list* of running and completed jobs, described in [Reviewing Puppet jobs](./orchestrator_job_status_view.html).

>**Note**: The first time you run a command, you need to authenticate. See the [orchestrator client install page](./orchestrator_install.html#set-pe-rbac-permissions-and-token-authentication-for-puppet-orchestrator) for information about setting RBAC permissions and token authorization.

The `run` action (`puppet job run`) initiates the orchestration job. It creates a new Job ID, shows you all nodes included in the job, and proceeds to run Puppet on all nodes in the appropriate order.

## Using the `puppet job run` command

Use the `puppet job run` command to trigger the Puppet orchestrator to create a new job and run Puppet on all included nodes. You can run jobs on one of three types of targets:

### To run a job across an entire environment

   `puppet job run <OPTIONS> <ENVIRONMENT>`

   If you don't specify any options or an environment, Puppet will run in order on all nodes in a Puppet environment (the default environment is `production`). 
   
   Note that if you want to run Puppet on all nodes in a given environment, Application Orchestration services must be enabled for that environment, but the nodes **DO NOT** need to be hosting application components. However, review the note about [environment mismatches on Puppet job runs](./orchestrator_install.html#environment-mismatches-on-puppet-job-runs) for details on what to expect if an agent detects an environment mismatch.
 
### To run a job on all instances of an application in an environment

   `puppet job run <APPLICATION> <OPTIONS>`

   In this case, Puppet runs in order on all nodes that are found in each instance of an application.

### To run a job that will configure a single application instance in an environment

   `puppet job run <APPLICATION INSTANCE> <OPTIONS>`

   In this case, Puppet runs in order on all nodes found in a specific application instance.

> **Note**: The default environment is `production`. This can be changed in the client config file (`~/.puppetlabs/client-tools/orchestrator.conf`). Additionally, you can override this with the `--env or -e` option.

### Command options

The following are common options you may use with the `run` action. For a complete list of global options run `puppet job --help`.

Option | Values | Description
-----------|-----------|-----------
`--service-url`  | flag | Overrides the URL setting for the orchestrator service.
`--noop` | flag, default false |  Run a deployment job without executing the changes.
- `--environment`, `--env`, `-e`| environment name | Overrides the environment specified in your orchestrator configuration file.
`--concurrency`, | flag | Limits how many nodes can run concurrently. (Default is unlimited.) You can [tune concurrent compile requests](./config_orchestration.html#tuning-orchestrator-concurrent-compile-requests) in the PE console.

## Review the job plan

>**Tip**: You can use the [`puppet-job plan` commmand](./orchestrator_job_plan.html) to print the plan that would be generated if you ran a Puppet job. The job plan shows application instances and the node run order that would be included in a job run with the same options.

When you execute a `puppet job run` command, the orchestrator service shows the plan for the run and immediately runs the job.

The job plan shows:

- The job ID. A job ID is created when you run a Puppet job. It can be used to view jobs with the [`puppet-job show`](./orchestrator_job_status_view.html) command.
- The environment the job will run in.
- The target for the job.
- A list of application instances included in the job.
- The total number of nodes in the job.
- The maximum concurrency setting for the job. 
- A list of nodes sorted topographically, with components and application instances listed below each node. The node list is organized in levels by dependencies. Nodes shown at the top, Level 0, have no dependencies. Nodes in Level 1 have dependencies above it in Level 0. A node can run once Puppet has finished running on all its dependencies.


> **Note**: Job run summaries that do not execute applications will not show any information about applications and components.

### Example job plan

The following is an example plan summary for a job that will run on the entire `production` environment (the default environment). Nodes not participating in an application are displayed at level 0 as they have no dependencies and can run immediately.

<a href="./images/orchestrator_job_run.png"><img src="./images/orchestrator_job_run.png" alt="Job Plan" title="Click to enlarge"> (Click to enlarge)</a>

### Node run status

In addition to details about the job, the command returns a list of nodes in the run. Node runs can be **in progress**, **completed**, **skipped**, or **failed**.

- For a **completed** node run, the orchestrator prints the following information:
   - The configuration version.
   - The transaction ID.
   - A summary of resource events.
   - A link to the full node run report in the PE console.

- For an **in progress** node run, the orchestrator prints how many seconds ago the run started.

- For a **failed** node run, the orchestrator prints an error message indicating why the run failed. In this case, any additional runs will be **skipped**.

You can view the status of all running, completed, and failed jobs with the `puppet job show` command.

## Terminating jobs

To terminate a running job, use `CTRL+C`.

When you terminate a job, any Puppet runs that are underway will finish. Any runs that have not started will not start.

## Analyze changes and events in the PE console

The PE console helps you [investigate the current state of your infrastructure](./CM_events.html) by drilling down into failures and other changes. The [node graph](./CM_graph.html) provides a graph view of a node’s catalog to help you locate and solve problems by finding which resources are causing nodes to fail or which resources have changed.

Use these tools for a closer look at successes and failures in your Orchestration runs.
