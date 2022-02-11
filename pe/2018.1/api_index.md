# API index

APIs allow you to interact with Puppet and Puppet Enterprise \(PE\) applications from your own code or application integration hooks.

## Puppet Enterprise APIs

For information on port requirements, see the [System Configuration](https://puppet.com/docs/pe/2019.1/system_configuration.html) documents.

|API|Useful for|
|---|----------|
|[RBAC service API v1](rbac_api_v1.md)|-   Managing access to Puppet Enterprise.
-   Connecting to external directories.
-   Generating authentication tokens.
-   Managing users, user roles, user groups, and user permissions.

|
|[RBAC service API v2](rbac_api_v2_endpoints.md)|-   Revoking authentication tokens.

|
|[Node classifier service API](node_classifier_service_api.md)|-   Querying the groups that a node matches.
-   Querying the classes, parameters, and variables that have been assigned to a node or group.
-   Querying the environment that a node is in.

|
|[Orchestrator API](orchestrator_api_v1_endpoints.md)|-   Gathering details about the orchestrator jobs you run.
-   Inspecting applications and applications instances in your Puppet environments.

|
|[Code Manager API](code_manager_api.md#)

|-   Creating a webhook to trigger Code Manager.
-   Queueing Puppet code deployments.
-   Checking Code Manager and file sync status.

|
|[Status API](status_api.md)|-   Checking the health status of PE services.

|
|[Activity service API](activity_api.md)|-   Querying PE service and user events logged by the activity service.

|
|[Razor API](using_the_razor_api.md#)|-   Provisioning bare-metal machines.

|

## Open source Puppet Server, Puppet, PuppetDB, and Forge APIs

|API|Useful for|
|---|----------|
|Puppet Server administrative API endpoints

-   [`environment-cache`](https://puppet.com/docs/puppetserver/5.3/admin-api/v1/environment-cache.html)

-   [`jruby-pool`](https://puppet.com/docs/puppetserver/5.3/admin-api/v1/jruby-pool.html)


|-   Deleting environment caches created by a Puppet master.
-   Deleting the Puppet Server pool of JRuby instances.

|
|Server-specific Puppet API -   [Environment classes](https://puppet.com/docs/puppetserver/5.3/puppet-api/v3/environment_classes.html)

-   [Environment modules](https://puppet.com/docs/puppetserver/5.3/puppet-api/v3/environment_modules.html)

-   [Static file content](https://puppet.com/docs/puppetserver/5.3/puppet-api/v3/static_file_content.html)


|-   Getting the classes and parameter information that is associated with an environment, with cache support.

-   Getting information about what modules are installed in an environment.

-   Getting the contents of a specific version of a file in a specific environment.


|
|[Puppet Server status API ](https://puppet.com/docs/puppetserver/5.3/status-api/v1/services.html)|-   Checking the state, memory usage, and uptime of the services running on Puppet Server.


|
|Puppet Server metrics API  -   [v1 metrics](https://puppet.com/docs/puppetserver/5.3/metrics-api/v1/metrics_api.html) \(deprecated\)

-   [v2 metrics](https://puppet.com/docs/puppetserver/5.3/metrics-api/v2/metrics_api.html) \(Jolokia\)


|-   Querying Puppet Server performance and usage metrics. 


|
|[Puppet HTTP API](https://puppet.com/docs/puppet/5.5/http_api/http_api_index.html)|-   Retrieving a catalog for a node
-   Accessing environment information

 For information on how this API is used internally by Puppet, see [Agent/Master HTTPs Communications page](https://puppet.com/docs/puppet/5.5/subsystem_agent_master_comm.html)

|
|[Certificate Authority \(CA\) API](https://puppet.com/docs/puppet/5.5/http_api/http_certificate.html)|-   Used internally by Puppet to manage agent certificates.

 See [Agent/Master HTTPs Communications](https://puppet.com/docs/puppet/5.5/subsystem_agent_master_comm.html) for details. See [Puppet's Commands page](https://puppet.com/docs/puppet/5.5/services_commands.html#puppet-cert) for information about the Puppet Cert command line tool.

|
|[PuppetDB APIs](https://puppet.com/docs/puppetdb/5.2/api/index.html)|-   Querying the data that PuppetDB collects from Puppet
-   Importing and exporting PuppetDB archives
-   Changing the PuppetDB model of a population
-   Querying information about the PuppetDB server
-   Querying PuppetDB metrics

|
|[Forge API](https://forgeapi.puppetlabs.com/)|-   Finding information about modules and users on the Forge
-   Writing scripts and tools that interact with the Forge website

|

