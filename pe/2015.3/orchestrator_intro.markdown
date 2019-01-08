---
layout: default
title: "Puppet Orchestrator Introduction"
canonical: "/pe/latest/orchestrator_intro.html"
---

The Puppet orchestrator is a set of interactive command line tools that provide the interface to the Application Orchestration service. These tools include `puppet job` and `puppet app`.

Whether or not you are managing applications, the Puppet orchestrator gives you the ability to control when Puppet runs and where node catalogs are applied (on the environment level). You no longer need to wait on arbitrary run times to update your nodes.

## Installing and Configuring

See [Installing the Puppet Orchestrator](./orchestrator_install.html) for instructions on installing, enabling, and configuring the Puppet orchestrator and the Application Orchestration service.

## `puppet job`

The `puppet job` tool allows you to manage and enforce ordered Puppet agent runs across a Puppet environment. You define the order of the agent runs by instantiating an application model and assigning nodes to application components (in your 'site.pp' file). 

After you've instantiated one or more applications and assigned components to nodes, you run the 'puppet-job' tool to apply each node's catalog in the appropriate order. 

If you aren't managing any applications in an environment, you can still use the `puppet job` tool to run Puppet on all nodes in that environment. 

> **Note**: Puppet will compile a new catalog for all nodes included in the job. 

For a complete reference of the `puppet job` command, see:

- [Running Puppet Jobs](./orchestrator_job_run.html)
- [Checking Status and Viewing Puppet Jobs](./orchestrator_job_status_view.html)

## `puppet app`

The `puppet app` tool lets you view the application models and application instances you've written and stored on your Puppet master, so you can see what's available to include in an orchestration run. 

For a complete reference of the `puppet app` command, see [Reviewing Applications](./orchestrator_app.html).


<!-- Ethnio Activation Code -->
<script type="text/javascript" language="javascript" src="//ethn.io/77154.js" async="true" charset="utf-8"></script>