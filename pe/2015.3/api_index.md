---
layout: default
title: "Puppet APIs"
canonical: "/pe/latest/api_index.html"
---

Puppet provides a number of APIs that allow you to interact with Puppet applications. On this page, you'll find a brief description of each API to help you figure out when to use which API.

## PuppetDB APIs

PuppetDB comes with a number of APIs. The PuppetDB Query API is used to query the data that PuppetDB collects from Puppet. You can query information such as:

* nodes
* node facts
* node resources
* environments
* catalogs
* reports
* events

There are also APIs for importing/exporting PuppetDB archives, changing PuppetDB's model of a population, querying information about the PuppetDB server, and querying PuppetDB metrics.

For a full list of PuppetDB APIs and endpoints, see [PuppetDB API Overview](/puppetdb/3.2/api/index.html).

## Role-Based Access Control (RBAC) Service API 

Use the RBAC Service API to manage access to Puppet Enterprise, including connecting to external directories, generating authentication tokens, and managing users, user roles, user groups, and user permissions.

For detailed information and RBAC Service API endpoints, see [RBAC Endpoints](./rbac_serviceindex.html).

## Node Classifier Service API

Use the Node Classifier Service API to query node classification data, such as the groups that a node matches, the classes, parameters, and variables that have been assigned to a node or group, and the environment that a node is in.

For detailed information and Node Classifier Service API endpoints, see [Node Classifier Endpoints](./nc_index.html).

## Code Manager API 

Use the Code Manager API to:

* Create a custom URL that is used to trigger Code Manager.
* Queue Puppet code deploys.

For information about the `webhook` endpoint, see [Webhook Endpoint](./code_mgr_webhook.html#webhook-endpoint).

For information about the `deploy` endpoint, see [Deploys Endpoint](./code_mgr_scripts.html#deploys-endpoint).

## Puppet Server Administrative API

You can use this API to:

* Delete environment caches created by a Puppet master.
* Delete Puppet Server's pool of JRuby instances.

For detailed information and the Administrative API endpoints, see the [`environment-cache` endpoint documentation](/puppetserver/2.2/admin-api/v1/environment-cache.html) and the [`jruby-pool` endpoint documentation](/puppetserver/2.2/admin-api/v1/jruby-pool.html).

## Status API

Use the Status API to check the health status of PE services. For detailed information and Status API endpoints, see [Monitoring PE Services](./status_api.html).

## Activity Service API

Use the Activity Service API to query the events that are logged by the Activity service. The Activity service logs Puppet Enterprise events by service and user.

## Razor API

Use the Razor API to provision bare-metal machines. For detailed information and the Razor API endpoints, see [Razor API Reference](./razor_reference.markdown).

## Forge API

Use the Forge API to get information about modules and users on the Puppet Forge. Using the Forge API, you can write scripts and tools that interact with the Forge website. For detailed information and Forge API endpoints, see [Puppet Forge API](https://forgeapi.puppetlabs.com).

## Certificate Authority (CA) API

This API is used internally by Puppet to manage Puppet agent certificates. For detailed information and CA API endpoints, see the [CA API documentation](/puppet/4.3/reference/http_api/http_certificate.html). For information about how this API is used internally by Puppet, see the [Agent/Master HTTPs Communications page](/puppet/4.3/reference/subsystem_agent_master_comm.html). For information about the Puppet Cert command line tool, see the [Puppet's Commands page](/puppet/4.3/reference/services_commands.html#puppet-cert).

## Puppet API

This API is used by the Puppet agent in communication with the Puppet master. Although it is primarily used internally by Puppet services, you can use it to get a catalog for a node, and to get environment information. For detailed information and Puppet API endpoints, see the [Puppet API documentation](/puppet/4.3/reference/http_api/http_api_index.html). For information about how this API is used internally by Puppet, see the [Agent/Master HTTPs Communications page](/puppet/4.3/reference/subsystem_agent_master_comm.html).

## File Sync

The File Sync API has endpoints for committing code to a Master of Masters and synchronizing code with compile masters. 

> **Important Note:** Puppet only supports file sync through use of [Code Manager](./code_mgr.html). Directly accessing the file sync API is not supported and not recommended.

For detailed information and File Sync API endpoints, see [File Sync API](./cmgmt_filesync_api.html).