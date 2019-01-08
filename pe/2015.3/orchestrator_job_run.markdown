---
layout: default
title: "Running Puppet Jobs"
canonical: "/pe/latest/orchestrator_job_run.html"
---

Two actions are available with the puppet-job command: 

- Run a job, as described in this topic.
- View a list of running and completed jobs, described in [Checking and Viewing Puppet Jobs](./orchestrator_job_status_view.html).

>**Note**: The first time you run a command, you need to authenticate. See the [Orchestrator client install page](./orchestrator_install.html#set-pe-rbac-permissions-and-token-authorization-for-puppet-orchestrator) for information about setting RBAC permissions and token authorization.

The `run` action (`puppet job run`) initiates the Orchestration job. It creates a new Job ID, shows you all nodes and application components included in the job, and proceeds to run puppet on all nodes in the appropriate order.

## Execute the `puppet job run` Command

Use the `puppet jobs run` command to trigger the Puppet orchestrator to create a new job and run Puppet across all included nodes. You can run jobs at one of three types of targets:

- To run a job across an entire environment:
   
   `puppet job run <OPTIONS>`
   
   If you don't specify any options, Puppet will run in order on all nodes in a Puppet environment (the default environment is `production`).

- To run a job on all instances of an application in an environment: 

   `puppet job run <APPLICATION> <OPTIONS>`
   
   In this case, Puppet runs in order on all nodes that are found in each instance of an application.

- To run a job that will configure a single application instance in an environment:

   `puppet job run <APPLICATION INSTANCE> <OPTIONS>`
   
   In this case, Puppet runs in order on all nodes found in a specific application instance.

> **Note**: The default environment is `production`. This can be changed in the client config file (`~/.puppetlabs/etc/puppet/orchestrator.conf`). Additionally, you can override this with the `--env or -e` option.

### Command Options

The following are common options you may use with the `run` action. For a complete list of global options run `puppet job --help`.

Option | Values | Description
-----------|-----------|-----------
`--url`  | flag | Overrides the URL setting for the orchestrator service.
`--noop` | flag, default false |  Run a deployment job without executing the changes.
- `--environment`, `--env`, `-e`| environment name | Overrides the environment specified in your orchestrator configuration file.
`--concurrency`, | flag | Sets the total number of nodes to run at a time, per job, while respecting dependencies. (Default is 50; there is no maximum.)

## Review the Job Plan  

When you execute a `puppet job run` command, the orchestrator service presents a plan and immediately runs the job.

The job plan shows:

- The number of application instances with a sublist of instance names.
- The total number of nodes that have application components.
- The total number of nodes in the run.
- The concurrency setting for the job. The concurrency default is 50 jobs. 
- A list of nodes sorted topographically, with components and application instances listed below each node. The node list is organized in levels by dependencies. Nodes shown at the top, Level 0, have no dependencies. Nodes in Level 1 have dependencies above it in Level 0. A node can run once Puppet has finished running on all its dependencies. 

> **Note**: Job run summaries that do not execute applications will not show any information about applications and components.

### Example Job Plan

The following is an example plan summary for a job that will run on the entire `production` environment (the default environment). Nodes not participating in an application are displayed at level 0 as they have no dependencies and can run immediately. 

<a href="./images/orchestrator_job_plan.png"><img src="./images/orchestrator_job_plan.png" alt="Job Plan" title="Click to enlarge"> (Click to enlarge)</a>

At this point the service creates a job ID and notifies you when:

1. A Puppet run starts on each node.  
2. A Puppet run finishes on each node.

When each node's run completes, Puppet prints the configuration version and a summary of resource events (changed, unchanged, failed, and skipped) for each node.
 
You can view the status of all running, completed, and failed jobs with the [`puppet-job list` command](./orchestrator_job_status_view.html).

## Terminating Jobs

To terminate a running job, use `CTRL+C`.

When you terminate a job, any Puppet runs that are underway will finish. Any runs that have not started will not start. 

## Analyze Changes and Events in the PE Console

The PE console helps you [investigate the current state of your infrastructure](./CM_events.html) by drilling down into failures and other changes. The [node graph](./CM_overview.html#view-the-node-graph) provides a graph view of a node’s catalog to help you locate and solve problems by finding which resources are causing nodes to fail or which resources have changed. 

Use these tools for a closer look at successes and failures in your Orchestration runs. 

<!-- Ethnio Activation Code -->
<script type="text/javascript" language="javascript" src="//ethn.io/77154.js" async="true" charset="utf-8"></script>