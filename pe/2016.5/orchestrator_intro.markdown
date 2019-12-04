---
layout: default
title: "Enforcing change with Puppet orchestrator: overview"
canonical: "/pe/latest/orchestrator_intro.html"
---

The Puppet orchestrator is a set of interactive command line tools that give you the ability to control the rollout of configuration changes when and how you want them---in other words, "enforce change." 

By enforcing change with Puppet orchestrator, you control when Puppet runs and where node catalogs are applied (from the environment level to an individual node). You no longer need to wait on arbitrary run times to update your nodes.

In PE, these on-demand runs are known as "jobs."

## Puppet orchestrator overview

The orchestrator includes the `puppet job` and `puppet app` tools. 

### `puppet job`

The `puppet job` tool allows you to manage and enforce ordered Puppet agent runs across an environment, against a PQL nodes query, on a list of nodes, or to enforce ordered agent runs by instantiating an application model and assigning nodes to application components (in your `site.pp` file).

For a complete reference of the `puppet job` command, see:

- [Checking Puppet job plans](./orchestrator_job_plan.html)
- [Running Puppet jobs](./orchestrator_job_run.html)
- [Reviewing Puppet jobs](./orchestrator_job_status_view.html)

### `puppet app`

The `puppet app` tool lets you view the application models and application instances you've written and stored on your Puppet master, so you can see what's available to include in an orchestration run.

For a complete reference of the `puppet app` command, see [Reviewing applications](./orchestrator_app.html).

### Installing and configuring the orchestrator

See [Configuring Puppet Orchestrator](./orchestrator_install.html) for instructions on installing and configuring the Puppet orchestrator.

### Direct Puppet: a workflow for controlling change

The Puppet orchestrator—used alongside other PE tools, such as Code Manager—allows you to control when and how infrastructure changes are made before they reach your production environment. See the [Direct Puppet workflow](./direct_puppet_workflow.html) for more information.

### Puppet orchestrator API

The [orchestrator API](./orchestrator_api_endpoints.html) consists of a number of endpoints that allow you to control orchestrator jobs and retrieve details about jobs, events, and applications available in your Puppet environments. 





