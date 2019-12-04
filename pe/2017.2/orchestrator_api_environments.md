---
layout: default
title: "Puppet orchestrator API: environments endpoint"
canonical: "/pe/latest/orchestrator_api_environments.html"
---

Use the `/environments` endpoint to retrieve information about your Puppet environments and any applications or application instances they contain.

## GET /environments

Retrieve a list of all Puppet environments.

### Response format

The response is a JSON object that lists the environments and provides details about them, and uses these keys:

Key | Definition
----|------------  
`id` | An absolute URL that links to the given environment
`name` | The name of the requested environment.
`applications` | A link to return all of the applications within the given environment.
`settings` | The various settings that exist within the environment.

For example:

```json
{
  "items" : [
    {
      "id" : "https://orchestrator.example.com:8143/orchestrator/v1/environments/production",
      "name" : "production",
      "applications" : { "id" : "https://orchestrator.example.com:8143/orchestrator/v1/environments/production/applications" },
      "settings" : {
        "modulepath" : [ "/path/to/puppet/modules", "/path/to/more/modules" ],
        "manifest" : "/path/to/puppet/code/environments/production/manifests",
        "environment_timeout" : 0,
        "config_version" : ""
      }
    },
    ...
  ]
}
```

## GET /environments/:environment

Retrieve information related to the requested Puppet environment.

### Response format

The response is a JSON object that provides details about the requested environment, and uses these keys:

Key | Definition
----|-----------
`id` | An absolute URL that links to the given environment
`name` | The name of the requested environment.
`applications` | A link to return all of the applications within the given environment.
`settings` | The various settings that exist within the environment.

For example:

```json
{
  "name" : "production",
  "id"   : "https://orchestrator.example.com:8143/orchestrator/v1/environments/production"
  "applications" : { "id" : "https://orchestrator.example.com:8143/orchestrator/v1/environments/production/applications" },
  "settings" : {
    "modulepath" : [ "/path/to/puppet/modules", "/path/to/more/modules" ],
    "manifest" : "/path/to/puppet/code/environments/production/manifests",
    "environment_timeout" : 0,
    "config_version" : ""
  }
}
```

### Error responses

See the [error response documentation](./orchestrator_api_errors.html) for the general format of error responses. For this endpoint, the `kind` key of the error displays the conflict.

Key | Definition
----|-----------
`puppetlabs.orchestrator/unknown-environment` | If the requested environment is not found, the server returns a `404` response.

## GET /environments/:environment/applications

Retrieve a list of applications that exist in a specific Puppet environment. 

### Response format

The response is a JSON object that lists the applications in the requested environment, and uses these keys:

Key | Definition
----|-----------
`instances` | A link to show all given application instances within the requested environment.
`items` | An array containing the various components related to the application.

For example:

```json
{
  "items" : [ {
    "line" : 1,
    "file" : "/opt/puppetlabs/puppet/modules/lamp/manifests/init.pp",
    "parameters" : {
      "db_user" : null,
      "db_password" : null,
      "web_count" : "2"
    },
    "name" : "Lamp",
    "kind" : "application"
  }, {
    "line" : 1,
    "file" : "/opt/puppetlabs/puppet/modules/forge/manifests/init.pp",
    "parameters" : {
      "db_user" : null,
      "db_password" : null
    },
    "name" : "Forge",
    "kind" : "application"
  }, {
    "line" : 1,
    "file" : "/opt/puppetlabs/puppet/modules/forge/manifests/tiered.pp",
    "parameters" : {
      "db_user" : null,
      "db_password" : null,
      "api_count" : "2",
      "web_count" : "2"
    },
    "name" : "Forge::Tiered",
    "kind" : "application"
  } ],
  "instances" : {
    "id" : "https://orchestrator.example.com:8143/orchestrator/v1/environments/lamp/instances"
  }
}
```

### Error response

See the [error response documentation](./orchestrator_api_errors.html) for the general format of error responses. For this endpoint, the `kind` key of the error displays the conflict.

Key | Definition
----|-----------
`puppetlabs.orchestrator/unknown-environment` | If the requested environment is not found, the server returns a `404` response.

## GET /environments/:environment/instances

Returns a list of all application instances within an environment. The components within each application instance are sorted topologically.

### Response format

The response is a JSON object that lists the applications in the requested environment, and uses this key:

Key | Definition
----|-----------
`items` | An array containing information about each application instance and its components within the `:environment`.

For example:

```json
{
  "items" : [ {
    "type" : "Lamp",
    "title" : "example",
    "components" : [ {
      "produces" : [ "Sql[example]" ],
      "consumes" : [ ],
      "node" : "1.example.com",
      "type" : "Site::Pql",
      "title" : "example"
    }, {
      "produces" : [ "Http[lamp-example-0]" ],
      "consumes" : [ ],
      "node" : "2.example.com",
      "type" : "Site::Apache",
      "title" : "example-0"
    }, {
      "produces" : [ "Http[lamp-example-2]" ],
      "consumes" : [ ],
      "node" : "4.example.com",
      "type" : "Site::Apache",
      "title" : "example-2"
    }, {
      "produces" : [ "Http[lamp-example-1]" ],
      "consumes" : [ ],
      "node" : "3.example.com",
      "type" : "Site::Apache",
      "title" : "example-1"
    }, {
      "produces" : [ "Http[lamp-lb-example]" ],
      "consumes" : [ "Http[lamp-example-0]", "Http[lamp-example-1]", "Http[lamp-example-2]" ],
      "node" : "5.example.com",
      "type" : "Site::Lb",
      "title" : "lamp-lb-example"
    } ]
  } ]
}
```

### Error responses

See the [error response documentation](./orchestrator_api_errors.html) for the general format of error responses. For this endpoint, the `kind` key of the error displays the conflict.

Key | Definition
----|------------
`puppetlabs.orchestrator/unknown-environment` | If the requested environment is not found, the server returns a `404` response.
