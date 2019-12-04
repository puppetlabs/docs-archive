# API index

APIs allow you to interact with Puppet and Puppet Enterprise \(PE\) applications. Descriptions of each API's core tasks will help you figure out when to use which API.

|API|Useful for|
|---|----------|
|[PuppetDB APIs](https://puppet.com/docs/puppetdb/5.1/api/index.html)|-   Querying the data that PuppetDB collects from Puppet
-   Importing and exporting PuppetDB archives
-   Changing the PuppetDB model of a population
-   Querying information about the PuppetDB server
-   Querying PuppetDB metrics

|
|[RBAC service API v1](rbac_api_v1.md)|-   Managing access to Puppet Enterprise
-   Connecting to external directories
-   Generating authentication tokens
-   Managing users, user roles, user groups, and user permissions

|
|[RBAC service API v2](rbac_api_v2_endpoints.md)|-   Revoking authentication tokens

|
|[Node classifier service API](node_classifier_service_api.md)|-   Querying the groups that a node matches
-   Querying the classes, parameters, and variables that have been assigned to a node or group
-   Querying the environment that a node is in

|
|[Puppet orchestrator API](orchestrator_api_v1_endpoints.md)|-   Gathering details about the orchestrator jobs you run
-   Inspecting applications and applications instances in your Puppet environments

|
|Code Manager API

-   [`webhook` endpoint](code_mgr_webhook.md#)

-   [`deploys` endpoint](code_mgr_scripts.md#)

-   [`status` endpoint](code_mgr_scripts.md#)


|-   Creating a custom URL that is used to trigger Code Manager
-   Queueing Puppet code deploys
-   Checking Code Manager and file sync status

|
|Puppet Server administrative API

-   [`environment-cache` endpoint](https://puppet.com/docs/puppetserver/5.1/admin-api/v1/environment-cache.html)

-   [`jruby-pool` endpoint](https://puppet.com/docs/puppetserver/5.1/admin-api/v1/jruby-pool.html)


|-   Deleting environment caches created by a Puppet master
-   Deleting the Puppet Server pool of JRuby instances

|
|[Status API](status_api.md)|-   Checking the health status of PE services

|
|[Activity service API](activity_api.md)|-   Querying PE service and user events logged by the activity service

|
|[Razor API](api_reference.md#)|-   Provisioning bare-metal machines

|
|[Forge API](https://forgeapi.puppetlabs.com/)|-   Finding information about modules and users on the Forge
-   Writing scripts and tools that interact with the Forge website

|
|[Certificate Authority \(CA\) API](https://puppet.com/docs/puppet/5.3/http_api/http_certificate.html)|Used internally by Puppet to manage Puppet agent certificates. See also:

-   [Agent/Master HTTPs Communications page](https://puppet.com/docs/puppet/5.3/subsystem_agent_master_comm.html) for information about how this API is used internally by Puppet
-   [Puppet's Commands page](https://puppet.com/docs/puppet/5.3/services_commands.html#puppet-cert) for information about the Puppet Cert command line tool

|
|[Puppet HTTP API](https://puppet.com/docs/puppet/5.3/http_api/http_api_index.html)|-   Retrieving a catalog for a node
-   Accessing environment information

For more information on how this API is used internally by Puppet, see [Agent/Master HTTPs Communications page](https://puppet.com/docs/puppet/5.3/subsystem_agent_master_comm.html)

|

