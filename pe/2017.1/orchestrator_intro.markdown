---
layout: default
title: "Enforcing change with Puppet orchestrator: overview"
canonical: "/pe/latest/orchestrator_intro.html"
---

The Puppet orchestrator is a set of interactive command line tools that give you the ability to control the rollout of configuration changes when and how you want them---in other words, "enforce change." 

By enforcing change with Puppet orchestrator, you control when Puppet runs and where node catalogs are applied (from the environment level to an individual node). You no longer need to wait on arbitrary run times to update your nodes.

In PE, these on-demand runs are known as "jobs."

## Puppet orchestrator command line tool

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

## Puppet orchestrator technical overview

The Puppet orchestrator uses pe-orchestration-services, a JVM-based service in PE, to execute on-demand Puppet runs on agent nodes in your infrastructure. The orchestrator uses PXP agents to orchestrate changes across your infrastructure.

The Puppet orchestrator (as part of pe-orchestration-services) controls the functionality for the `puppet-job` and `puppet-app` commands, as well as controlling the functionality for the **Run Puppet** button in the PE console.

The Puppet orchestrator is comprised of several components, each with their own configuration and log locations.

### What happens during a Puppet orchestrator job?

Several services interact when you run an orchestrator job.

1. You use the `puppet-job` command to create a job in orchestrator.
2. The orchestrator validates your token with the PE RBAC service.
3. The orchestrator requests environment classification from the node classifier for the nodes targeted in the job, and it queries PuppetDB for the nodes.
5. The orchestrator requests the environment graph from Puppet Server.
6. The orchestrator creates the job ID and starts polling nodes in the job to check their statuses.
7. The orchestrator queries PuppetDB for the puppet agent version on the nodes targeted in the job.
8. The orchestrator tells the PCP broker to start Puppet runs on the nodes targeted in the job, and Puppet runs start on those agents.
9. The Puppet agent sends its run results to the PCP broker.
10. The orchestrator receives run results, and requests the node run reports from PuppetDB.

### Puppet orchestrator architecture

The functionality of the Puppet orchestrator is derived from the Puppet Execution Protocol and the Puppet Communications Protocol. 

- [PXP](https://github.com/puppetlabs/pcp-specifications/tree/master/pxp/versions/1.0): A message format used to request that a task be executed on a remote host and receive responses on the status of that task. This is used by the pe-orchestration services to run Puppet on agents.

- PXP agent: A system service in the puppet-agent package that runs PXP.

- [PCP](https://github.com/puppetlabs/pcp-specifications/tree/master/pcp/versions/2.0): The underlying communication protocol that describes how PXP messages get routed to an agent and back to the orchestrator.

- PCP broker: A JVM-based service, within pe-orchestration-services, that routes PCP messages. PCP messages are fairly simple: they declare the content of the message via message type, and identify the sender and intended recipient. PXP agents running on the Puppet agents connect to the broker via websockets and wait for messages to be sent to them. The pe-orchestration-services connect to the PCP broker as well, and sends requests to the broker that are routed to specific agents.

### Configuration notes for the orchestrator and related components

Configuration and tuning for the components in the orchestrator happen in various files. 

- pe-orchestration-services: The underlying service for the Puppet orchestrator and the PCP broker. The main configuration file is `/etc/puppetlabs/orchestration-services/conf.d`. 

   PCP broker authorization is managed by PE via trapperkeeper-authorization.

   Additional configuration for large infrastructures may include [tuning the pe-orchestration-services JVM heap size](/config_java_args.html#increase-the-java-heap-size-for-pe-java-services), [increasing the limit on open file descriptors for pe-orchestration-services](./config_ulimit.html#configure-ulimit-for-pe-services), and [tuning ARP tables](./config_orchestration.html#correcting-arp-table-overflow).

   The PCP broker requires JVM memory and file descriptors, and these resources scale linearly with the number of active connections. Specifically, the PCP broker requires:
   
    - approximately 40 KB of memory (when restricted with the `-Xmx` JVM option)
    
    - one file descriptor per connection
    
    - an approximate baseline of 60 MB of memory and 200 file descriptors 
    
   For a deployment of 100 agents, expect to configure the JVM with at least `-Xmx64m` and 300 file descriptors. Message handling requires minimal additional memory.

- PXP agent: Configuration is managed by the Agent profile in PE (puppet_enterprise::profile::agent).

   The PXP agent is configured to use Puppet’s SSL certificates and point to one PCP broker endpoint. If high availability (HA) is configured, the agent will point to additional PCP broker endpoints in the case of failover.    
   

### Debugging the orchestrator and related components

If you need to debug the orchestrator or any of its related components, the following log locations may be helpful. 

- pe-orchestration-services: The main log file is `/var/log/puppetlabs/orchestration-services/orchestration-services.log`. 

   PCP-related logging can be controlled by the `puppetlabs.pcp` path in the logback configuration. Optionally, you can [enable an access log](https://github.com/puppetlabs/pcp-broker/blob/master/doc/logging.md#pcp-access-log) for messages.

- PXP agent: The main log file is `/var/log/puppetlabs/pxp-agent/pxp-agent.log` (on *nix) or `C:/ProgramData/PuppetLabs/pxp-agent/var/log/pxp-agent.log` (on Windows). 

   Additionally, metadata about Puppet runs triggered via the PXP agent are kept in the spool-dir, which defaults to `/opt/puppetlabs/pxp-agent/spool` (on *nix) and or `C:/ProgramData/PuppetLabs/pxp-agent/var/spool` (on Windows). Results are kept for 14 days.


********
**Related links**

- Installing and configuring the orchestrator

   See [Configuring Puppet Orchestrator](./orchestrator_install.html) for instructions on installing and configuring the Puppet orchestrator.

- Direct Puppet: a workflow for controlling change

   The Puppet orchestrator—used alongside other PE tools, such as Code Manager—allows you to control when and how infrastructure changes are made before they reach your production environment. See the [Direct Puppet workflow](./direct_puppet_workflow.html) for more information.

- Puppet orchestrator API

   The [orchestrator API](./orchestrator_api_endpoints.html) consists of a number of endpoints that allow you to control orchestrator jobs and retrieve details about jobs, events, and applications available in your Puppet environments. 





