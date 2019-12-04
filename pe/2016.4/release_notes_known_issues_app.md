---
layout: default
title: "Orchestration services known issues"
canonical: "/pe/latest/release_notes_known_issues_app.html"
---

This page lists known issues for orchestration services in Puppet Enterprise.


## Orchestrator jobs on Windows agents can't be terminated with `CTRL+C`

If you need to stop an orchestration job running on a Windows agent, you can use the [/command/stop API endpoint](./orchestrator_api_commands.html#post-commandstop). <!--ORCH-1080-->


## Agents in Puppet job runs may request incorrect environment

When you use the Puppet orchestrator `puppet job` command with the `environment` flag, Puppet agent runs participating in the job should request the specified environment. A known issue exists where these agent runs may request a catalog for a different environment than the one specified in the job.

To workaround this issue, we recommend that you [use the node classifier in the PE console](./console_classes_groups.html#adding-nodes-to-a-node-group) to add agents to the environment that will be participating in Orchestration jobs. Alternatively, you could [assign the agent's environment in its configuration file]({{puppet}}/environments_assigning.html#assigning-environments-via-the-agents-config-file).









