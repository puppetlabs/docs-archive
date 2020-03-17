# PE architecture

Puppet Enterprise \(PE\) is made up of various components and services including the master and compilers, the Puppet agent, console services, Code Manager and r10k, orchestration services, and databases.

The following diagram shows the architecture of a typical PE installation.

![A typical PE installation showing a master, compile masters, console services, and managed nodes.](pe_architecture.png)

**Related information**  


[Component versions in recent PE releases](component_versions_in_recent_pe_releases.md#)

[PE and open source version numbers](pe_and_open_source_version_numbers.md)

## The master and compilers

The Puppet master is the central hub of activity and process in Puppet Enterprise. This is where code is compiled to create agent catalogs, and where SSL certificates are verified and signed.

PE infrastructure components are installed on a single node: the master. The master always contains a compiler and a Puppet Server. As your installation grows, you can add additional compilers to distribute the catalog compilation workload.

Each compiler contains the Puppet Server, the catalog compiler, and an instance of file sync.

### Puppet Server

Puppet Server is an application that runs on the Java Virtual Machine \(JVM\) on the master. In addition to hosting endpoints for the certificate authority service, it also powers the catalog compiler, which compiles configuration catalogs for agent nodes, using Puppet code and various other data sources.

### Catalog compiler

To configure a managed node, the agent uses a document called a catalog, which it downloads from the master or a compiler. The catalog describes the desired state for each resource that should be managed on the node, and it can specify dependency information for resources that should be managed in a certain order.

### File sync

File sync keeps your code synchronized across multiple compilers. When triggered by a web endpoint, file sync takes changes from the working directory on the master and deploys the code to a live code directory. File sync then deploys that code to any compilers, ensuring that all masters in a multi-master configuration are kept in sync, and that your code is deployed only when it's ready.

### Certificate Authority

The internal [certificate authority \(CA\) service](https://puppet.com/docs/puppetserver/latest/services_master_puppetserver.html#certificate-authority-service) accepts certificate signing requests \(CSRs\) from nodes, serves certificates and a certificate revocation list \(CRL\) to nodes, and optionally accepts commands to sign or revoke certificates.

The CA service uses `.pem` files in the standard [ssldir](https://puppet.com/docs/puppet/6.10/dirs_ssldir.html) to store credentials. You can use the `puppetserver ca` command to interact with these credentials, including listing, signing, and revoking certificates.

**Note:** Depending on your architecture and security needs, the CA can be hosted either on the master or on its own node. The CA service on compilers is configured, by default, to proxy CA requests to the CA.

**Related information**  


[Hardware requirements](hardware_requirements.md#)

[Installing compilers](installing_compilers.md)

[About file sync](filesync_about.md#)

## The Puppet agent

Managed nodes run the Puppet agent application, usually as a background service. The master and any compilers also run a Puppet agent.

Periodically, the agent sends facts to a master and requests a catalog. The master compiles the catalog using several sources of information, and returns the catalog to the agent.

After it receives a catalog, the agent applies it by checking each resource the catalog describes. If it finds any resources that are not in their desired state, it makes the changes necessary to correct them. \(Or, in no-op mode, it reports on what changes would have been made.\)

After applying the catalog, the agent submits a report to its master. Reports from all the agents are stored in PuppetDB and can be accessed in the console.

Puppet agent runs on \*nix and Windows systems.

-   [Puppet Agent on \*nix Systems](https://puppet.com/docs/puppet/6.10/services_agent_unix.html)
-   [Puppet Agent on Windows Systems](https://puppet.com/docs/puppet/6.10/services_agent_windows.html)

### Facter

[Facter](https://puppet.com/docs/facter/3.12/) is the cross-platform system profiling library in Puppet. It discovers and reports per-node facts, which are available in your Puppet manifests as variables.

Before requesting a catalog, the agent uses Facter to collect system information about the machine it’s running on.

For example, the fact `os` returns information about the host operating system, and `networking` returns the networking information for the system. Each fact has various elements to further refine the information being gathered. In the `networking` fact, `networking.hostname` provides the hostname of the system.

Facter ships with a built-in list of [core facts](https://puppet.com/docs/facter/3.12/core_facts.html), but you can build your own custom facts if necessary.

You can also use facts to determine the operational state of your nodes and even to group and classify them in the NC.

## Console services

The console services includes the console, role-based access control \(RBAC\) and activity services, and the node classifier.

### The console

The console is the web-based user interface for managing your systems.

The console can:

-   browse and compare resources on your nodes in real time.
-   analyze events and reports to help you visualize your infrastructure over time.
-   browse inventory data and backed-up file contents from your nodes.
-   group and classify nodes, and control the Puppet classes they receive in their catalogs.
-   manage user access, including integration with external user directories.

The console leverages data created and collected by PE to provide insight into your infrastructure.

### RBAC

In PE, you can use RBAC to manage user permissions. Permissions define what actions users can perform on designated objects.

For example:

-   Can the user grant password reset tokens to other users who have forgotten their passwords?
-   Can the user edit a local user's role or permissions?
-   Can the user edit class parameters in a node group?

The RBAC service can connect to external LDAP directories. This means that you can create and manage users locally in PE, import users and groups from an existing directory, or do a combination of both. PE supports OpenLDAP and Active Directory.

You can interact with the RBAC and activity services through the console. Alternatively, you can use the RBAC service API and the activity service API. The activity service logs events for user roles, users, and user groups.

PE users generate tokens to authenticate their access to certain command line tools and API endpoints. Authentication tokens are used to manage access to the following PE services and tools: Puppet orchestrator, Code Manager , Node Classifier, role-based access control \(RBAC\), and the activity service.

Authentication tokens are tied to the permissions granted to the user through RBAC, and provide users with the appropriate access to HTTP requests.

### Node classifier

PE comes with its own node classifier \(NC\), which is built into the console.

Classification is when you configure your managed nodes by assigning classes to them. **Classes** provide the Puppet code—distributed in modules—that enable you to define the function of a managed node, or apply specific settings and values to it. For example, you might want all of your managed nodes to have time synchronized across them. In this case, you would group the nodes in the NC, apply an NTP class to the group, and set a parameter on that class to point at a specific NTP server.

You can create your own classes, or you can take advantage of the many classes that have already been created by the Puppet community. Reduce the potential for new bugs and to save yourself some time by using existing classes from modules on the [Forge](https://forge.puppetlabs.com/), many of which are approved or supported by Puppet, Inc.

You can also classify nodes using the NC API.

**Related information**  


[Monitoring current infrastructure state](monitor_infrastructure_state.md#)

[Managing access](managing_access.md)

[Endpoints](rbac_api_v1_endpoints.md#)

[Activity service API](activity_api.md)

[Token endpoints](rbac_api_v1_token.md#)

[Setting PE RBAC permissions and token authentication for orchestrator](configuring_puppet_orchestrator.md#)

[Request an authentication token for deployments](code_mgr_config.md#)

[Authenticating to the node classifier API](forming_node_classifier_requests.md#)

[Forming RBAC API requests](rbac_api_v1_forming_requests.md#)

[Forming activity service API requests](activity_api_forming_requests.md#)

[User permissions and user roles](rbac_permissions_intro.md#)

[Grouping and classifying nodes](grouping_and_classifying_nodes.md#)

[Node classifier API v1](node_classifier_service_api.md)

## Code Manager and r10k

PE includes tools for managing and deploying your Puppet code: Code Manager and r10k.

These tools install modules, create and maintain [environments](https://puppet.com/docs/puppet/6.10/environments_about.html), and deploy code to your masters, all based on code you keep in Git. They sync the code to your masters, so that all your servers start running the new code at the same time, without interrupting agent runs.

Both Code Manager and r10k are built into PE, so you don't have to install anything, but you need to have a basic familiarity with Git.

Code Manager comes with a command line tool which you can use to trigger code deployments from the command line.

**Related information**  


[Managing and deploying Puppet code](managing_puppet_code.md)

[Triggering Code Manager on the command line](puppet_code.md#)

[Running jobs with Puppet orchestrator](running_jobs_with_puppet_orchestrator_overview.md#)

## Orchestration services

Orchestration services is the underlying toolset that drives Puppet Application Orchestration and the Puppet orchestrator.

Puppet Application Orchestration provides Puppet language extensions and command-line tools to help you configure and manage multi-service and multi-node applications. Specifically, application orchestration is:

-   Puppet language elements for describing configuration relationships between components of a distributed application.

For example, in a three-tier stack application infrastructure—a load-balancer, an application/web server, and a database server—these servers have dependencies on one another. You want the application server to know where the database service is and how they connect, so that you can cleanly bring up the application. You then want the load balancer to automatically configure itself to balance demand on a number of application servers. And if you update the configuration of these machines, or roll out a new application release, you want the three tiers to reconfigure in the correct order

-   A service that orchestrates ordered configuration enforcement from the node level to the environment level.

The orchestrator is a command-line tool for planning, executing, and inspecting orchestration jobs. For example, you can use it to review application instances declared in an environment, or to enforce change on the environment level without waiting for nodes to check in in regular 30-min intervals.

The orchestration service interacts with PuppetDB to retrieve facts about nodes. To run orchestrator jobs, users must first authenticate to Puppet Access, which verifies their user and permission profile as managed in RBAC.

## PE databases

PE uses PostgreSQL as a database backend. You can use an existing instance, or PE can install and manage a new one.

The PE PostgreSQL instance includes the following databases:

|Database|Description|
|--------|-----------|
|pe-activity|Activity data from the Classifier, including who, what and when|
|pe-classifier|Classification data, all node group information|
|pe-puppetdb|Exported resources, catalogs, facts, and reports \(see more, below\)|
|pe-rbac|Users, permissions, and AD/LDAP info|
|pe-orchestrator|Details about job runs, users, nodes, and run results|

### PuppetDB

PuppetDB collects data generated throughout your Puppet infrastructure. It enables advanced features like exported resources, and is the database from which the various components and services in PE access data. Agent run reports are stored in PuppetDB.

See the PuppetDB overview for more information.

**Related information**  


[Database maintenance](pe_database_maintenance.md#)

## Security and communications

The services and components in PE use a variety of communication and security protocols.

|Service/Component|Communication Protocol|Authentication|Authorization|
|-----------------|----------------------|--------------|-------------|
|Puppet Server|HTTPS, SSL/TLS|SSL certificate verification with Puppet CA|trapperkeeper-auth|
|Certificate Authority|HTTPS, SSL/TLS|SSL certificate verification with Puppet CA|trapperkeeper-auth|
|Puppet agent|SSL/TLS|SSL certificate verification with Puppet CA|n/a|
|PuppetDB|HTTPS externally, or HTTP on the loopback interface|SSL certificate verification with Puppet CA|SSL certificate whitelist|
|PostgreSQL|PostgreSQL TCP, SSL for PE|SSL certificate verification with Puppet CA|SSL certificate whitelist|
|Activity service|SSL|SSL certificate verification with Puppet CA, token authentication|RBAC user-based authorization|
|RBAC|SSL|SSL certificate verification with Puppet CA, token authentication|RBAC user-based authorization|
|Classifier|SSL|SSL certificate verification with Puppet CA, token authentication|RBAC user-based authorization|
|Console Services UI|SSL|Session-based authentication|RBAC user-based authorization|
|Orchestrator|HTTPS, Secure web sockets|RBAC token authentication|RBAC user-based authorization|
|PXP agent|Secure web sockets|SSL certificate verification with Puppet CA|n/a|
|PCP broker|Secure web sockets|SSL certificate verification with Puppet CA|trapperkeeper-auth|
|File sync|HTTPS, SSL/TLS|SSL certificate verification with Puppet CA|trapperkeeper-auth|
|Code Manager|HTTPS; can fetch code remotely via HTTP, HTTPS, and SSH \(via Git\)|RBAC token authentication; for remote module sources, HTTP\(S\) Basic or SSH keys|RBAC user-based authorization; for remote module sources, HTTP\(S\) Basic or SSH keys|
|Razor|HTTPS, SSL|Apache Shiro authentication|Apache Shiro users and roles|

