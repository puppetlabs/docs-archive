---
layout: default
title: "Orchestration services known issues"
canonical: "/pe/latest/release_notes_known_issues_app.html"
---

This page lists known issues for the Puppet orchestrator and orchestration services in Puppet Enterprise.


## **New:** PCP brokers don't wait for pxp-agent to reconnect

In this version of PE, the PCP broker uses immediate message delivery. If an agent node is disconnected, PXP will send 2 status requests in a row, each time immediately receiving a response that the agent is not connected, leaving no time for the pxp-agent on the agent node to restart and reconnect. This will be fixed in the next major version. <!--ORCH-1637-->

## Orchestrator jobs on Windows agents can't be terminated with `CTRL+C`

If you need to stop an orchestration job running on a Windows agent, you can use the [/command/stop API endpoint](./orchestrator_api_commands.html#post-commandstop). <!--ORCH-1080-->


## Agents in Puppet job runs may request incorrect environment

When you use the Puppet orchestrator `puppet job` command with the `environment` flag, Puppet agent runs participating in the job should request the specified environment. A known issue exists where these agent runs may request a catalog for a different environment than the one specified in the job.

To workaround this issue, we recommend that you [use the node classifier in the PE console](./console_classes_groups.html#adding-nodes-to-a-node-group) to add agents to the environment that will be participating in Orchestration jobs. Alternatively, you could [assign the agent's environment in its configuration file]({{puppet}}/environments_assigning.html#assigning-environments-via-the-agents-config-file).









