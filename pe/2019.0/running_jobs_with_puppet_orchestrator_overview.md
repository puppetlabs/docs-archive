# Running jobs with Puppet orchestrator

With the Puppet orchestrator, you can run two types of "jobs": on-demand Puppet runs or Puppet tasks.

When you run Puppet on-demand with the orchestrator, you control the rollout of configuration changes when and how you want them. You control when Puppet runs and where node catalogs are applied \(from the environment level to an individual node\). You no longer need to wait on arbitrary run times to update your nodes.

Puppet tasks allow you to execute actions on target machines. A "task" is a single action that you execute on the target via an executable file. For example, do you want to upgrade a package or restart a particular service? Set up a Puppet task job to enforce to make those changes at will.

Tasks are packaged and distributed as Puppet modules.

## Puppet orchestrator technical overview

The orchestrator uses pe-orchestration-services, a JVM-based service in PE, to execute on-demand Puppet runs on agent nodes in your infrastructure. The orchestrator uses PXP agents to orchestrate changes across your infrastructure.

The orchestrator \(as part of pe-orchestration-services\) controls the functionality for the `puppet-job` and `puppet-app` commands, and also controls the functionality for jobs and single node runs in the PE console.

The orchestrator is comprised of several components, each with their own configuration and log locations.

### Puppet orchestrator architecture

The functionality of the orchestrator is derived from the Puppet Execution Protocol \(PXP\) and the Puppet Communications Protocol \(PCP\).

-   PXP: A message format used to request that a task be executed on a remote host and receive responses on the status of that task. This is used by the pe-orchestration services to run Puppet on agents.

-   PXP agent: A system service in the agent package that runs PXP.

-   PCP: The underlying communication protocol that describes how PXP messages get routed to an agent and back to the orchestrator.

-   PCP broker: A JVM-based service that runs in pe-orchestration-services on the master of masters \(MoM\) and in the pe-puppetserver service on compile masters. PCP brokers route PCP messages, which declare the content of the message via message type, and identify the sender and intended recipient. PCP brokers on compile masters connect to the orchestrator, and the orchestrator uses the brokers to direct messages to PXP agents connected to the compile masters. When using compile masters, PXP agents running on PE components \(the Puppet master, PuppetDB, and the PE console\) connect directly to the orchestrator, but all other PXP agents connect to compile masters via load balancers.


### What happens during an on-demand run from the orchestrator ?

Several PE services interact when you run Puppet on demand from the orchestrator.

1.  You use the `puppet-job` command to create a job in orchestrator.
2.  The orchestrator validates your token with the PE RBAC service.
3.  The orchestrator requests environment classification from the node classifier for the nodes targeted in the job, and it queries PuppetDB for the nodes.
4.  The orchestrator requests the environment graph from Puppet Server.
5.  The orchestrator creates the job ID and starts polling nodes in the job to check their statuses.
6.  The orchestrator queries PuppetDB for the agent version on the nodes targeted in the job.
7.  The orchestrator tells the PCP broker to start runs on the nodes targeted in the job, and Puppet runs start on those agents.
8.  The agent sends its run results to the PCP broker.
9.  The orchestrator receives run results, and requests the node run reports from PuppetDB.

### What happens during a task run from the orchestrator?

Several services interact for a task run as well. Since tasks are Puppet code, they must be deployed into an environment on the Puppet master. Puppet Server then exposes the task metadata to the orchestrator. When a task is run, the orchestrator sends the PXP agent a URL of where to fetch the task from the master and the checksum of the task file. The PXP agent downloads the task file from the URL and caches it for future use. The file is validated against the checksum before every execution. The following are the steps in this process.

1.  The PE client sends a task command.
2.  The orchestrator checks if a user is authorized.
3.  The orchestrator fetches the node target from PuppetDB if the target is a query, and returns the nodes.
4.  The orchestrator requests task data from Puppet Server.
5.  Puppet Server returns task metadata, file URIs, and file SHAs.
6.  The orchestrator validates the task command and then sends the job ID back to the client.
7.  The orchestrator sends task parameters and file information to the PXP agent.
8.  The PXP agent sends a provisional response to the orchestrator, checks the SHA against the local cache, and requests the task file from Puppet Server.
9.  Puppet Server returns the task file to the PXP agent.
10. The task runs.
11. The PXP agent sends the result to the orchestrator.
12. The client requests events from the orchestrator.
13. The orchestrator returns the result to the client.

### Configuration notes for the orchestrator and related components

Configuration and tuning for the components in the orchestrator happens in various files.

-   pe-orchestration-services: The underlying service for the orchestrator. The main configuration file is `/etc/puppetlabs/orchestration-services/conf.d`.

    Additional configuration for large infrastructures might include tuning the pe-orchestration-services JVM heap size, increasing the limit on open file descriptors for pe-orchestration-services, and tuning ARP tables.


-   PCP broker: Part of the pe-puppetserver service. The main configuration file is `/etc/puppetlabs/puppetserver/conf.d`.

    The PCP broker requires JVM memory and file descriptors, and these resources scale linearly with the number of active connections. Specifically, the PCP broker requires:

    -   Approximately 40 KB of memory \(when restricted with the \`-Xmx\` JVM option\)

    -   One file descriptor per connection

    -   An approximate baseline of 60 MB of memory and 200 file descriptors

    For a deployment of 100 agents, expect to configure the JVM with at least `-Xmx64m` and 300 file descriptors. Message handling requires minimal additional memory.


-   PXP agent: Configuration is managed by the agent profile \(`puppet_enterprise::profile::agent`\).

    The PXP agent is configured to use Puppet’s SSL certificates and point to one PCP broker endpoint. If high availability \(HA\) is configured, the agent points to additional PCP broker endpoints in the case of failover.

    **Note:** If you reuse an existing agent with a new orchestrator instance, you must delete the pxp-agent spool directory, located at `/opt/puppetlabs/pxp-agent/spool` \(\*nix\) or `C:\ProgramData\PuppetLabs\pxp-agent\var\spool` \(Windows\)


### Debugging the orchestrator and related components

If you need to debug the orchestrator or any of its related components, the following log locations might be helpful.

-   pe-orchestration-services: The main log file is `/var/log/puppetlabs/orchestration-services/orchestration-services.log`.

-   PCP: The main log file for PCP brokers on compile masters is `/var/log/puppetlabs/puppetserver/pcp-broker.log`. You can configure logback through the Puppet server configuration.

    The main log file for PCP brokers on the MoM is `/var/log/puppetlabs/orchestration-services/pcp-broker.log`.

    You can also enable an access log for messages.


-   PXP agent: The main log file is `/var/log/puppetlabs/pxp-agent/pxp-agent.log` \(on \*nix\) or `C:/ProgramData/PuppetLabs/pxp-agent/var/log/pxp-agent.log` \(on Windows\). You can configure this location as necessary.

    Additionally, metadata about Puppet runs triggered via the PXP agent are kept in the spool-dir, which defaults to `/opt/puppetlabs/pxp-agent/spool` \(on \*nix\) and or `C:/ProgramData/PuppetLabs/pxp-agent/var/spool` \(on Windows\). Results are kept for 14 days.


