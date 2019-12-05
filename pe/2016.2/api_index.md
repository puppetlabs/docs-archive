---
layout: default
title: "Index of Puppet APIs"
canonical: "/pe/latest/api_index.html"
---

Puppet provides a number of APIs that allow you to interact with Puppet applications. On this page, you'll find a brief description of each API to help you figure out when to use which API.

## PuppetDB APIs

PuppetDB comes with a number of APIs. The PuppetDB query API is used to query the data that PuppetDB collects from Puppet. You can query information such as:

* nodes
* node facts
* node resources
* environments
* catalogs
* reports
* events

There are also APIs for importing/exporting PuppetDB archives, changing PuppetDB's model of a population, querying information about the PuppetDB server, and querying PuppetDB metrics.

For a full list of PuppetDB APIs and endpoints, see [PuppetDB API overview]({{puppetdb}}/api/index.html).

## Role-based access control (RBAC) service APIs

Use the RBAC service API v1 to manage access to Puppet Enterprise, including connecting to external directories, generating authentication tokens, and managing users, user roles, user groups, and user permissions.

For detailed information and RBAC service API v1 endpoints, see [RBAC v1 endpoints](./rbac_serviceindex_v1.html).

The RBAC service API v2 features the ability to revoke authentication tokens.

For detailed information and RBAC service API v2 endpoints, see [RBAC v2 endpoints](./rbac_serviceindex_v2.html).

## Node classifier service API

Use the node classifier service API to query node classification data, such as the groups that a node matches, the classes, parameters, and variables that have been assigned to a node or group, and the environment that a node is in.

For detailed information and node classifier service API endpoints, see [Node classifier endpoints](./nc_index.html).

## Puppet orchestrator API

Use the Puppet orchestrator API to gather details about the orchestrator jobs you run and to inspect applications and applications instances in your Puppet environments.

For detailed information about the orchestrator API v1 endpoints, see [Puppet orchestrator endpoints](./orchestrator_api_endpoints.html).

## Code Manager API

Use the Code Manager API to:

* Create a custom URL that is used to trigger Code Manager.
* Queue Puppet code deploys.

For information about the `webhook` endpoint, see [Webhook endpoint](./code_mgr_webhook.html#webhook-endpoint).

For information about the `deploy` endpoint, see [Deploys endpoint](./code_mgr_scripts.html#deploys-endpoint).

## Puppet Server administrative API

You can use this API to:

* Delete environment caches created by a Puppet master.
* Delete Puppet Server's pool of JRuby instances.

For detailed information and the administrative API endpoints, see the [`environment-cache` endpoint documentation]({{puppetserver}}/admin-api/v1/environment-cache.html) and the [`jruby-pool` endpoint documentation]({{puppetserver}}/admin-api/v1/jruby-pool.html).

## Status API

Use the status API to check the health status of PE services. For detailed information and status API endpoints, see [Monitoring PE services](./status_api.html).

## Activity service API

Use the activity service API to query the events that are logged by the activity service. The activity service logs Puppet Enterprise events by service and user.

## Razor API

Use the Razor API to provision bare-metal machines. For detailed information and the Razor API endpoints, see the [Razor API reference](./razor_reference.markdown).

## Forge API

Use the Forge API to get information about modules and users on the Puppet Forge. Using the Forge API, you can write scripts and tools that interact with the Forge website. For detailed information and Forge API endpoints, see [Puppet Forge API](https://forgeapi.puppetlabs.com).

## Certificate Authority (CA) API

This API is used internally by Puppet to manage Puppet agent certificates. For detailed information and CA API endpoints, see the [CA API documentation]({{puppet}}/http_api/http_certificate.html). For information about how this API is used internally by Puppet, see the [Agent/Master HTTPs Communications page]({{puppet}}/subsystem_agent_master_comm.html). For information about the Puppet Cert command line tool, see the [Puppet's Commands page]({{puppet}}/services_commands.html#puppet-cert).

## Puppet API

This API is used by the Puppet agent in communication with the Puppet master. Although it is primarily used internally by Puppet services, you can use it to get a catalog for a node, and to get environment information. For detailed information and Puppet API endpoints, see the [Puppet API documentation]({{puppet}}/http_api/http_api_index.html). For information about how this API is used internally by Puppet, see the [Agent/Master HTTPs Communications page]({{puppet}}/subsystem_agent_master_comm.html).

## File sync API

The file sync API has endpoints for committing code to a Master of Masters and synchronizing code with compile masters.

> **Important Note:** Puppet only supports file sync through use of [Code Manager](./code_mgr.html). Directly accessing the file sync API is not supported and not recommended.

For detailed information and file sync API endpoints, see [our guide to the file sync API](./cmgmt_filesync_api.html).