---
layout: default
title: "PE architecture overview"
toc_levels: 123
canonical: "/pe/latest/pe_architecture_overview.html"
---

The following diagram shows the architecture of the services and components that comprise Puppet Enterprise. The various components and services are described below. 

<a href="./images/pe_architecture.png"><img src="./images/pe_architecture.png" alt="PE architecture diagram" title="Click to enlarge"> (Click to enlarge)</a>


# The master of masters (MoM)

The master of masters (MoM)---also known as the Puppet master---is the central hub of activity and process in Puppet Enterprise. This is where Puppet code is compiled to create agent catalogs, and where SSL certificates are verified and signed. 

You can install PE in one of two ways: monolithic or split. In a monolithic installation, the MoM hosts all services on one node. For a split installation, the services related to the MoM, the PE console, and PuppetDB (with PostgreSQL) are each hosted on separate nodes.

Regardless of the installation architecture, the MoM always contains a compile master and a Puppet Server. As your installation grows, you can add additional compile masters to distribute the catalog compilation workload.

See the latest [system requirements](./install_system_requirements.html) for more information about choosing a PE installation architecture. 

These are the core services and components associated with the MoM and compile masters.

## Compile masters

Each compile master contains the Puppet Server, the catalog compiler, and an instance of file sync. As your infrastructure grows, you can [add compile masters](./install_multimaster.html) to expand processing capabilities. 

### Puppet Server

Puppet Server is an application that runs on the Java Virtual Machine (JVM) on the MoM. In addition to hosting endpoints for the certificate authority service, it also powers the catalog compiler, which compiles configuration catalogs for Puppet agent nodes, using Puppet code and various other data sources. See the Puppet Server docs for more information about [Puppet Server services]({{puppetserver}}/services_master_puppetserver.html#puppet's-services:-puppet-server).

### Catalog compiler

To configure a managed node, Puppet agent uses a document called a catalog, which it downloads from the MoM or a compile master. The catalog describes the desired state for each resource that should be managed on the node, and it can specify dependency information for resources that should be managed in a certain order. 

### File sync

[File sync](cmgmt_filesync.html) keeps your Puppet code synchronized across multiple compile masters. When triggered by a web endpoint, file sync takes changes from the working directory on the MoM and deploys the code to a live code directory. File sync then deploys that code onto all your compile masters, ensuring that all masters in a multi-master configuration are kept in sync, and that your Puppet code is deployed only when it is ready.

See the [file sync API docs](./cmgmt_filesync_api.html) for information about interacting with this service. 

File sync uses [Git](https://git-scm.com/about) as its version control backend.

### Certificate Authority

Puppet's internal [certificate authority (CA) service]({{puppetserver}}/services_master_puppetserver.html#certificate-authority-service) accepts certificate signing requests (CSRs) from nodes, serves certificates and a certificate revocation list (CRL) to nodes, and optionally accepts commands to sign or revoke certificates.

The CA service uses `.pem` files in the standard Puppet [ssldir]({{puppet}}/dirs_ssldir.html) to store credentials. You can use the standard `puppet cert` command to interact with these credentials, including listing, signing, and revoking certificates.

>**A few notes about the Puppet CA**
>
> - Depending on your architecture and security needs, the CA can be hosted either on the MoM or on its own node. The CA service is **always** disabled on additional compile masters. 
>
> - You can [replace Puppet's CA with an external CA](./external_ca). 

### Puppet agent

The MoM and any compile master you install will include the Puppet agent.

# Puppet agent

Managed nodes run the Puppet agent application, usually as a background service. Managed nodes include the MoM and any compile masters.

Periodically, Puppet agent will send facts to a master and request a catalog. The master compiles the catalog using several sources of information, and returns the catalog to the agent.

Once it receives a catalog, Puppet agent applies it by checking each resource the catalog describes. If it finds any resources that are not in their desired state, it will make any changes necessary to correct them. (Or, in no-op mode, it will report on what changes would have been needed.)

After applying the catalog, Puppet agent submits a report to its master. Reports from all the agents are stored in PuppetDB and can be accessed in the PE console.

Puppet agent runs on *nix and Windows systems. 

- [Puppet Agent on *nix Systems]({{puppet}}/services_agent_unix.html)
- [Puppet Agent on Windows Systems]({{puppet}}/services_agent_windows.html)

Puppet agent includes the following components:

## MCollective

[MCollective](./orchestration_overview.html) is a distributed task-based orchestration system. Nodes with MCollective listen for commands over a message bus and independently take action when they hear an authorized request. This lets you investigate and command your infrastructure in real time without relying on a central inventory.

## Facter

[Facter]({{facter}}/latest.html) is Puppet’s cross-platform system profiling library. It discovers and reports per-node facts, which are available in your Puppet manifests as variables.

Before requesting a catalog, Puppet agent uses [Facter]({{facter}}/latest.html) to collect system information about the machine it’s running on.
 
For example the fact `os` returns information about the host operating system, and `networking` returns the networking information for the system. Each fact has various elements to further refine the information being gathered. In the `networking` fact, `networking.hostname` provides the hostname of the system. 

Facter ships with a built-in list of [core facts]({{facter}}/core_facts.html#facter:-core-facts), but you can build your own custom facts if necessary. 

You can also use facts to determine the operational state of your nodes and even to group and classify them in the NC. 
 
# PE Services

The following services enable you to make the most of PE's functionality and tooling. These services are inter-related and provide a flexible and powerful arsenal for managing your infrastructure with Puppet Enterprise. 

Unlike the previously described services and components that run on the masters and agents, which are also available in open source Puppet, these services are available to Puppet Enterprise customers only. 

## Console services

The PE console services includes the following:


- The PE console UI
- Role-based access control (RBAC) and activity services
- The classifier

### PE console UI

The PE console is the web-based user interface for managing your systems. The console can:

- browse and compare resources on your nodes in real time.
- analyze events and reports to help you visualize your infrastructure over time.
- browse inventory data and backed-up file contents from your nodes.
- group and classify nodes, and control the Puppet classes they receive in their catalogs.
- manage user access, including integration with external user directories.

[Introducing configuration management](./CM_overview.html#introducing-configuration-management) explains how the console leverages data created and collected by PE to provide insight into your infrastructure.

### RBAC 

In PE, you can RBAC to manage user permissions. Permissions define what actions users can perform on designated objects. For example:

- Can the user grant password reset tokens to other users who have forgotten their passwords?

- Can the user edit a local user's role or permissions?

- Can the user edit class parameters in a node group?

The RBAC service can connect to external LDAP directories. This means that you can create and manage users locally in PE, import users and groups from an existing directory, or do a combination of both. PE supports OpenLDAP and Active Directory.

For more information about managing access to PE, see the [RBAC intro](./rbac_intro.html).

#### RBAC and activity services API

You can interact with the RBAC and activity services through the PE console. Alternatively, you can use the [RBAC service API](./rbac_serviceindex_v1.html) and the [activity service API](./rbac_activityapis.html). The activity service logs events for user roles, users, and user groups.

#### Puppet access (token auth)

PE users [generate tokens to authenticate their access](./rbac_token_auth.html) to certain PE command line tools and API endpoints. Authentication tokens are used to manage access to the following PE services and tools:

* [Puppet orchestrator](./orchestrator_install.html#set-pe-rbac-permissions-and-token-authentication-for-puppet-orchestrator)
* [Code Manager](./code_mgr_webhook.html#request-an-rbac-token)
* [Node Classifier](./nc_forming_requests.html#authentication-token)
* [Role-based access control (RBAC)](./rbac_forming_requests_v1.html#authentication-token)
* [Activity service](./rbac_activityapis.html#authentication-token)

Authentication tokens are tied to the [permissions granted to the user through RBAC](./rbac_permissions.html), and provide users with the appropriate access to HTTP requests.

## Classifier

PE comes with its own node classifier (NC), which is built into the PE console. 

### What is classification?

Classification is when you configure your managed nodes by assigning classes to them. **Classes** provide the Puppet code—distributed in modules—that enable you to define the function of a managed node, or apply specific settings and values to it. For example, you may want all your managed nodes to have time synchronized across them. In this case, you would group the nodes in the NC, apply an NTP class to the group, and set a parameter on that class to point at a specific NTP server. The act of grouping nodes in this way is what we call classification. 
 
You can create your own classes, or you can take advantage of the many classes that have already been created by the Puppet community. To reduce the potential for new bugs and to save yourself some time by using existing classes, see the modules on the [Puppet Forge](https://forge.puppetlabs.com/), many of which are approved or supported by Puppet Labs.

See [Getting started with classification](http://docs.puppetlabs.com/pe/latest/console_classes_groups.html) to learn more about using the PE console NC to manage your nodes.

You can also classify nodes using the [NC API](./nc_index.html).

## Code Manager and r10k 

PE includes tools for managing and deploying your Puppet code---Code Manager and r10k. These tools install Puppet modules, create and maintain [environments]({{puppet}}/environments.html), and deploy code to your masters, all based on code you keep in Git. They sync the code to your masters, so that all your servers start running the new code at the same time, without interrupting agent runs.

Both Code Manager and r10k are built into PE, so you don't have to install anything---but you will need to have a basic familiarity with Git. For more about these code management tools, see [Managing and Deploying Puppet Code](./cmgmt_managing_code.html).
 
Code Manager comes with a command line tool which you can use to [trigger code deployments from the command line](./code_mgr_cli.html). 

## Orchestration services

Orchestration services is what we call the underlying toolset that drives Puppet Application Orchestration and the Puppet orchestrator. 

[Puppet Application Orchestration](./app_orchestration_overview.html) provides Puppet language extensions and command-line tools to help you configure and manage multi-service and multi-node applications. Specifically, application orchestration is:

- Puppet language elements for describing configuration relationships between components of a distributed application. 

For example, in a three-tier stack application infrastructure—a load-balancer, an application/web server, and a database server—these servers have dependencies on one another. You want the application server to know where the database service is and how they connect, so that you can cleanly bring up the application. You then want the load balancer to automatically configure itself to balance demand on a number of application servers. And if you update the configuration of these machines, or roll out a new application release, you want the three tiers to reconfigure in the correct order

- A service that orchestrates ordered configuration enforcement from the node level to the environment level. For example ...

The [Puppet orchestrator](./orchestrator_intro.html) is a command-line tool for planning, executing, and inspecting orchestration jobs. For example, you can use it to  review application instances declared in an environment, or to enforce change on the environment level without waiting for nodes to check in in regular 30-min intervals. 

The orchestration service interacts with PuppetDB to retrieve facts about nodes. To run orchestrator jobs, users must first authenticate to Puppet access, which verifies their user and permission profile as managed in RBAC. 

# PE Databases

## PostgreSQL

Puppet Enterprise uses PostgreSQL as a database backend. You can use an existing instance, or PE can install and manage new one. The PE PostgreSQL instance includes the following databases:


Database      | Description
------------- | ---------------------------------------------------------------
pe-activity   | Activity data from the Classifier, including who, what and when
pe-classifier | Classification data, all Node Group information
pe-puppetdb   | exported resources, catalogs, facts, and reports (see more, below)
pe-rbac       | users, permissions, and AD/LDAP info
pe-orchestrator | details about job runs, users, nodes, and run results 

n[Maintaining the PE databases](./maintain_console-db.html) provides information about managing these databases.

## PuppetDB

PuppetDB collects data generated throughout your Puppet infrastructure. It enables advanced Puppet features like exported resources, and is the database from which the various components and services in PE access data. Puppet agent run reports are stored in PuppetDB.

See the [PuppetDB overview]({{puppetdb}}/puppetdb/latest/) for more about PuppetDB. 


