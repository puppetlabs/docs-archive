---
layout: default
title: "Puppet orchestrator: overview"
canonical: "/pe/latest/orchestrator_intro.html"
---

The Puppet orchestrator is a set of interactive command line tools that give you the ability to control the rollout of configuration changes when and how you want them. These tools include `puppet job` and `puppet app`.

Whether or not you are managing applications, with Puppet orchestrator, you control when Puppet runs and where node catalogs are applied (on the environment level). You no longer need to wait on arbitrary run times to update your nodes.

## Installing and configuring

See [Installing the Puppet Orchestrator](./orchestrator_install.html) for instructions on installing, enabling, and configuring the Puppet orchestrator and the Application Orchestration service.

## `puppet job`

The `puppet job` tool allows you to manage and enforce ordered Puppet agent runs across an environment, or enforce ordered agent runs by instantiating an application model and assigning nodes to application components (in your `site.pp` file).

> **Note**: Puppet will compile a new catalog for all nodes included in the job.

For a complete reference of the `puppet job` command, see:

- [Checking Puppet job plans](./orchestrator_job_plan.html)
- [Running Puppet jobs](./orchestrator_job_run.html)
- [Reviewing Puppet jobs](./orchestrator_job_status_view.html)

## `puppet app`

The `puppet app` tool lets you view the application models and application instances you've written and stored on your Puppet master, so you can see what's available to include in an orchestration run.

For a complete reference of the `puppet app` command, see [Reviewing applications](./orchestrator_app.html).

## Puppet orchestrator workflow

Depending on the policies and procedures across your team, the way you use the orchestrator may vary. In general, the workflow consists of the following steps:

1. Write Puppet code you intend to use with [Puppet Application Orchestration](./app_orchestration_workflow.html). Use the `puppet parser validate` command as needed.
2. Use the [`puppet app show` command](./orchestrator_app.html) to validate that your application or application instances looks correct.
3. Use the [`puppet job plan` command](./orchestrator_job_plan.html) to show applications or application instances and the node run order that would be included in a job. 
4. Use the [`puppet job run` command](./orchestrator_job_run.html) to enforce change on your infrastructure and configure your application. (Or run the job with the `--noop` flad to see what enforcement with the Puppet agent would entail). 
5. Use the [`puppet job show` command](./orchestrator_job_status_view.html) (or follow the report links provided after the command runs) to review more details about your job run.


