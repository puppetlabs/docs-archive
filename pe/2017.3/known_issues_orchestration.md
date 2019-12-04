# Orchestration services known issues

These are the known issues for the orchestration services in this release.

## Existing pxp-agents can't run jobs with new orchestrator instances

If you point an existing agent at a new orchestrator instance—for example, you move an agent to a new PE installation—the new orchestrator may reuse job IDs that are still in the pxp-agent's spool directory. In such cases, any time you run a new job, the agent reports the status of a prior job rather than running a new job.

To work around this issue, delete the contents of the pxp-agent's spool directory.

-   On \*nix agents: `/opt/puppetlabs/pxp-agent/spool`.

-   On Windows agents:`C:\ProgramData\PuppetLabs\pxp-agent\var\spool`


## Agents in job runs may request incorrect environment

When you use the orchestrator `puppet job` command with the `environment` flag, agent runs participating in the job might request a catalog for a different environment than the one specified in the job.

To work around this issue, use the node classifier in the console to add agents to the environment that are participating in orchestration jobs. Alternatively, you can assign the agent's environment in its configuration file.

