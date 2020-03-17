# Puppet orchestrator API: root endpoint

Use the `/orchestrator` endpoint to return metadata about the orchestrator API.

**Parent topic:**[Puppet orchestrator API v1 endpoints](orchestrator_api_v1_endpoints.md)

## GET /orchestrator

A request to the root of the Puppet orchestrator returns metadata about the orchestrator API, along with a list of links to application management resources.

### Response format

Responses use the following format:

```json
{
  "info" : {
    "title" : "Application Management API (EXPERIMENTAL)",
    "description" : "Multi-purpose API for performing application management operations",
    "warning" : "This version of the API is experimental, and might change in backwards-incompatible ways in the future",
    "version" : "0.1",
    "license" : {
      "name" : "Puppet Enterprise License",
      "url" : "https://puppetlabs.com/puppet-enterprise-components-licenses"
    }
  },
  "status" : {
    "name" : "status",
    "id" : "https://orchestrator.example.com:8143/orchestrator/v1/status"
  },
  "collections" : [ {
    "name" : "environments",
    "id" : "https://orchestrator.example.com:8143/orchestrator/v1/environments"
  }, {
    "name" : "jobs",
    "id" : "https://orchestrator.example.com:8143/orchestrator/v1/jobs"
  } ],
  "commands" : [ {
    "name" : "deploy",
    "id" : "https://orchestrator.example.com:8143/orchestrator/v1/command/deploy"
  }, {
    "name" : "stop",
    "id" : "https://orchestrator.example.com:8143/orchestrator/v1/command/stop"
  } ]
}
```

### Error responses

For this endpoint, the server returns a `500` response.

**Related information**  


[Puppet orchestrator API: error responses](orchestrator_api_error_responses.md)

