---
layout: default
title: "Enforcing change with Puppet orchestrator: overview"
canonical: "/pe/latest/orchestrator_intro.html"
---

The Puppet orchestrator gives you the ability to control the rollout of configuration changes when and how you want them---in other words, "enforce change." By enforcing change with Puppet orchestrator, you control when Puppet runs and where node catalogs are applied (from the environment level to an individual node). You no longer need to wait on arbitrary run times to update your nodes. 

In PE, these on-demand Puppet runs are known as "jobs."

## Tools for running orchestrator jobs

Orchestrator jobs can be run from the PE console or from the command line. 

### Orchestrator in the PE console

The orchestrator in the PE console gives you the ability to set up jobs in the console and enforce Puppet agent runs against a PQL nodes query or on a list of nodes. 

With console integration, you can use the console's reporting and infrastructure monitoring tools to review jobs and dig deeper into node runs.

See [Running jobs in the console](./orchestrator_job_run_console.html) for details.

### Orchestrator command line tool

The orchestrator command line tool includes the `puppet job` and `puppet app` commands. 

- `puppet job`

   The `puppet job` command is the main mechanism to control Puppet jobs. For a complete reference of the `puppet job` command, see:
   
   - [Checking job plans on the CLI](./orchestrator_job_plan.html)
   - [Running jobs on the CLI](./orchestrator_job_run.html)
   - [Reviewing jobs on the CLI](./orchestrator_job_status_view.html)

- `puppet app`

   The `puppet app` tool lets you view the application models and application instances you've written and stored on your Puppet master.
   
   For a complete reference of the `puppet app` command, see [Reviewing applications on the CLI](./orchestrator_app.html).

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

- PCP broker: A JVM-based service that runs in pe-orchestration-services on the master of masters (MoM) and in the pe-puppetserver service on compile masters. PCP brokers route PCP messages, which declare the content of the message via message type, and identify the sender and intended recipient. PCP brokers on compile masters connect to the orchestrator, and the orchestrator uses the brokers to direct messages to PXP agents connected to the compile masters. When using compile masters, PXP agents running on PE components (the Puppet master, PuppetDB, and the PE console) connect directly to the orchestrator, but all other PXP agents connect to compile masters via load balancers.

### Configuration notes for the orchestrator and related components

Configuration and tuning for the components in the orchestrator happen in various files. 

- pe-orchestration-services: The underlying service for the Puppet orchestrator. The main configuration file is `/etc/puppetlabs/orchestration-services/conf.d`. 

   Additional configuration for large infrastructures may include [tuning the pe-orchestration-services JVM heap size](./config_java_args.html#increase-the-java-heap-size-for-pe-java-services), [increasing the limit on open file descriptors for pe-orchestration-services](./config_ulimit.html#configure-ulimit-for-pe-services), and [tuning ARP tables](./config_orchestration.html#correcting-arp-table-overflow).

- PCP broker: Part of the pe-puppetserver service. The main configuration file is `/etc/puppetlabs/puppetserver/conf.d`

   The PCP broker requires JVM memory and file descriptors, and these resources scale linearly with the number of active connections. Specifically, the PCP broker requires:
   
    - approximately 40 KB of memory (when restricted with the `-Xmx` JVM option)
    
    - one file descriptor per connection
    
    - an approximate baseline of 60 MB of memory and 200 file descriptors 
    
   For a deployment of 100 agents, expect to configure the JVM with at least `-Xmx64m` and 300 file descriptors. Message handling requires minimal additional memory.

- PXP agent: Configuration is managed by the Agent profile in PE (`puppet_enterprise::profile::agent`).

   The PXP agent is configured to use Puppet’s SSL certificates and point to one PCP broker endpoint. If high availability (HA) is configured, the agent will point to additional PCP broker endpoints in the case of failover.    
   

### Debugging the orchestrator and related components

If you need to debug the orchestrator or any of its related components, the following log locations may be helpful. 

- pe-orchestration-services: The main log file is `/var/log/puppetlabs/orchestration-services/orchestration-services.log`. 

- PCP: The main log file for PCP brokers on compile masters is `/var/log/puppetlabs/puppetserver/pcp-broker.log`. You can [configure logback through the Puppet server configuration]({{puppetserver}}/configuration.html#logging). 

   The main log file for PCP brokers on the MoM is `/var/log/puppetlabs/orchestration-services/pcp-broker.log`.

   You can also [enable an access log](https://github.com/puppetlabs/pcp-broker/blob/master/doc/logging.md#pcp-access-log) for messages. 

- PXP agent: The main log file is `/var/log/puppetlabs/pxp-agent/pxp-agent.log` (on *nix) or `C:/ProgramData/PuppetLabs/pxp-agent/var/log/pxp-agent.log` (on Windows). You can [configure this location](./config_orchestration.html#configure-pxp-agent-logfile-location) as necessary.

   Additionally, metadata about Puppet runs triggered via the PXP agent are kept in the spool-dir, which defaults to `/opt/puppetlabs/pxp-agent/spool` (on *nix) and or `C:/ProgramData/PuppetLabs/pxp-agent/var/spool` (on Windows). Results are kept for 14 days.


********
**Related links**

- Running orchestrator jobs

   See [Running jobs in the console](./orchestrator_job_run_console.html) or [Running jobs from the command line](./orchestrator_job_run.html) for instructions on running jobs.
   
- Installing compile masters

  See [Compile master installation](./install_multimaster.html) for instructions on installing compile masters and configuring load balancers for PXP agents.    

- Direct Puppet: a workflow for controlling change

   The Puppet orchestrator—used alongside other PE tools, such as Code Manager—allows you to control when and how infrastructure changes are made before they reach your production environment. See the [Direct Puppet workflow](./direct_puppet_workflow.html) for more information.

- Puppet orchestrator API

   The [orchestrator API](./orchestrator_api_endpoints.html) consists of a number of endpoints that allow you to control orchestrator jobs and retrieve details about jobs, events, and applications available in your Puppet environments. 


