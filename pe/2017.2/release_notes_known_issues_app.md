---
layout: default
title: "Orchestration services known issues"
canonical: "/pe/latest/release_notes_known_issues_app.html"
---

This page lists known issues for orchestration services in Puppet Enterprise.

## Existing pxp-agents can't run jobs with new orchestrator instances

If you point an existing agent at a new orchestrator instance—for example, you move an agent to a new PE install—the new orchestrator may reuse job IDs that are still in the pxp-agent's spool directory. In such cases, anytime you run a new job, the agent will report the status of a prior job rather than running a new job.

1. To work around this issue, delete the contents of the pxp-agent's spool directory.

   - On *nix agents, `/opt/puppetlabs/pxp-agent/spool`.
   
   - On Windows agents, `C:\ProgramData\PuppetLabs\pxp-agent\var\spool`

<!--PCP-718-->

## Agents in Puppet job runs may request incorrect environment

When you use the Puppet orchestrator `puppet job` command with the `environment` flag, Puppet agent runs participating in the job should request the specified environment. A known issue exists where these agent runs may request a catalog for a different environment than the one specified in the job.

To workaround this issue, we recommend that you [use the node classifier in the PE console](./console_classes_groups.html#adding-nodes-to-a-node-group) to add agents to the environment that will be participating in Orchestration jobs. Alternatively, you could [assign the agent's environment in its configuration file]({{puppet}}/environments_assigning.html#assigning-environments-via-the-agents-config-file).









