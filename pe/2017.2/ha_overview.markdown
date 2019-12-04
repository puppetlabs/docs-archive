---
layout: default
title: "High availability overview"
canonical: "/pe/latest/ha_overview.html"

---

Enabling high availability for Puppet Enterprise ensures that your system remains operational even if certain infrastructure components become unreachable.

High availability configuration creates a replica of your *primary master*. The primary master is your Puppet master or, if your installation includes compile masters, your master of masters. The replica node is called the *primary master replica*. You may only have one provisioned replica at a time.

**Note:** In this release, we don't support high availability for split installations – where the master, console, and PuppetDB components are installed on separate machines. Only monolithic installations - with or without compile masters and a master of masters - is supported.

There are two main advantages to enabling high availability:

* If your primary master fails, the replica takes over, continuing to perform critical operations. For details, see [What happens during failovers](#what-happens-during-failovers).
* If your primary master can’t be repaired, you can promote the replica to master. Promotion establishes the replica as the new, permanent master.


## High availability architecture

The primary master replica is not an exact copy of the primary master. Rather, the replica duplicates specific infrastructure components and services. Hiera data and other custom configurations aren’t replicated.

Replication may be *read-write*, meaning that data can be written to the service or component on either the master or the replica, and the data is synced to both nodes. Alternatively, replication may be *read-only*, where data is written only to the master and synced to the replica. Some components and services, like Puppet server and the Console services UI, aren’t replicated because they contain no native data.

Some components and services are activated immediately when you enable a replica; others aren’t active until you promote a replica.

Component or service	   | Type of replication          | Activated when replica is...
--------------------------------|--------------------------------|--------------------
Puppet server		  | none 	                    | enabled
File sync client	  | read-only	                    | enabled
PuppetDB		  | read-write	                    | enabled
Certificate authority	  | read-only	                    | promoted
RBAC service		  | read-only	                    | enabled
Node classifier service  | read-only	                    | enabled
Activity service	  | read-only	                    | enabled
Orchestration service	  | read-only	                    | promoted
Console service UI	  | none	                    | promoted

For details about PE components and services, see the [PE architecture overview](./pe_architecture_overview.html).

**Monolithic HA configuration with a single master**

![Monolithic HA configuration with a single master](./images/ha_singlemaster.png)

In a monolithic installation, when Puppet runs fail over, agents communicate with the replica instead of the master.

**Monolithic HA configuration with compile masters**

![Monolithic HA configuration with compile masters](./images/ha_compilemasters.png)

In a monolithic installation with compile masters, agents communicate with load balancers or compile masters, which in turn communicate with the master or replica.


## What happens during failovers

*Failover* occurs when the replica takes over services usually performed by the master.

Failover is automatic, meaning that you don’t have to take action to activate the replica. With high availability enabled, Puppet runs are directed first to the master. If the primary master is either fully or partially unreachable, Puppet runs are directed to the replica.

In partial failovers, Puppet runs may use the server, node classifier, or PuppetDB on the replica if those services aren’t reachable on the master. For example, if the master’s node classifier fails, but its Puppet server is still running, agent runs use the Puppet server on the master but fail over to the replica’s node classifier.

What works during failovers:

* Scheduled Puppet runs
* Catalog compilation
* Viewing classification data using the node classifier API
* Reporting and queries based on PuppetDB data

What doesn’t work during failovers:

* Deploying new Puppet code
* Editing node classifier data
* Using the console
* Certificate functionality, including provisioning new agents, revoking certificates, or running the `puppet certificate` command
* Most CLI tools
* Application Orchestration


## System and software requirements

Your Puppet infrastructure must meet specific requirements in order to configure high availability.

<table>
  <tr>
    <th>Component</th>
    <th>Requirement</th>
  </tr>
  <tr>
    <td>Operating system</td>
    <td>
     <ul>
      <li>RedHat 7</li>
      <li>CentOS 7</li>
      <li>Oracle Linux 7</li>
      <li>Scientific Linux 7</li>
      <li>Ubuntu 16.04</li>
     </ul></td>
  </tr>
  <tr>
    <td>Software</td>
    <td>
     <ul>
      <li>You must use <a href="./code_mgr.html">Code Manager</a> so that code is deployed to both the master and the replica after you enable a replica.</li>
      <li>You must use the default PE node classifier so that high availability classification can be applied to nodes.</li>
      <li><a href="./orchestrator_intro.html">Orchestrator</a> must be enabled so that agents are updated when you provision or enable a replica. Orchestrator is enabled by default.</li>
     </ul></td>
  </tr>
  <tr>
    <td>Replica</td>
    <td>
     <ul>
      <li>Must be a Puppet agent node that doesn’t have a specific function already. For example, you can’t provision an existing master or compile master as a replica.</li>
      <li>Must have the same hardware specifications and capabilities as your primary master node.</li>
     </ul></td>
  </tr>
  <tr>
    <td>Firewall</td>
    <td>Both the master and the replica must comply with these port requirements:
     <ul>
      <li><a href="./sys_req_sysconfig.html#for-monolithic-installs-with-compile-masters">Firewall configuration for monolithic installations with compile masters</a>. These requirements apply whether your HA environment uses a single master or compile masters.</li>
      <li>Port 5432 must be open to facilitate database replication by console services.</li>
     </ul></td>
  </tr>
  <tr>
    <td>Node names</td>
    <td>
     <ul>
      <li>You must use resolvable domain names when specifying node name for the master and replica.</li>
     </ul></td>
  </tr>
  <tr>
    <td>RBAC tokens</td>
    <td>
     <ul>
      <li>You must have an admin RBAC token when running <code>puppet infrastructure</code> commands, including <code>provision</code>, <code>enable</code>, and <code>forget</code>. You can <a href="./rbac_token_auth.html#generating-a-token-using-puppet-access">generate a token</a>  using the <code>puppet-access</code> command.</li> <b>Note:</b> You don’t need an RBAC token to promote a replica.
     </ul></td>
  </tr>
</table>


## Classification changes in high availability installations

When you provision and enable a replica, the system makes a number of classification changes in order to manage high availability without affecting existing configuration.

These preconfigured node groups are added in high availability installations:

Node group   | Matching nodes              | Inherits from
------------------|--------------------------------|--------------------
PE HA Master    | primary master                | PE master node group
PE HA Replica   | primary master replicas   | PE infrastructure node group

These parameters are used to configure high availability installations:

<table>
  <tr>
    <th>Parameter</th>
    <th>Purpose</th>
    <th>Node group</th>
    <th>Classes</th>
    <th>Notes</th>
  </tr>
  <tr>
    <td>agent-server-urls</td>
    <td>Specifies the list of servers that Puppet agents contact, in order.</td>
    <td><p>PE Agent</p> <p>PE Infrastructure Agent</p></td>
    <td><code>puppet_enterprise::<br>profile::agent</code></td>
    <td><p>In monolithic installations with compile masters, agents must be configured to communicate with the load balancers or compile masters.</p> <p><b>Important:</b> Setting agents to communicate directly with the replica in order to use the replica as a compile master is not a supported configuration.</p></td>
  </tr>
  <tr>
    <td>replication_mode</td>
    <td>Sets replication type and direction on masters and replicas.</td>
    <td><p>PE master (<code>none</code>)</p> <p>HA master (<code>source</code>)</p> <p>HA replica (<code>replica</code>)</p></td>
    <td><p><code>puppet_enterprise::<br>profile::master</code></p> <p><code>puppet_enterprise::<br>profile::database</code></p> <p><code>puppet_enterprise::<br>profile::console</code></p></td>
    <td>System parameter. Don’t modify.</td>
  </tr>
    <tr>
    <td>ha_enabled_replicas</td>
    <td>Tracks replica nodes that are failover ready.</td>
    <td>PE Infrastructure</td>
    <td><code>puppet_enterprise</code></td>
    <td><p>System parameter. Don't modify.</p> <p>Updated when you enable a replica.</p></td>
  </tr>
  </tr>
    <tr>
    <td>pcp_broker_list</td>
    <td>Specifies the list of PCP brokers that PXP agents contact, in order.</td>
    <td><p>PE Agent</p> <p>PE Infrastructure Agent</p></td>
    <td><code>puppet_enterprise::<br>profile::agent</code></td>
    <td></td>
  </tr>
</table>


## Load balancer timeout in high availability installations

High availability configuration uses timeouts to determine when to fail over to the replica. If the load balancer timeout is shorter than the server and agent timeout, connections from agents might be terminated during failover.

To avoid timeouts, set the timeout option for load balancers to four minutes or longer. This duration allows compile masters enough time for required queries to PuppetDB and the node classifier service. You can set the load balancer timeout option using parameters in the [haproxy](https://forge.puppet.com/puppetlabs/haproxy?) or [f5](https://forge.puppet.com/puppetlabs/f5) modules.


* * *
