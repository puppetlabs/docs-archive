---
layout: default
title: "Puppet orchestrator API: error responses"
canonical: "/pe/latest/orchestrator_api_errors.html"
---


## Error response description

Every error response from the Puppet orchestrator will be a JSON response. Each response will be an object containing the following keys:

- `kind`: the kind of error encountered.
- `msg`: the message associated with the error.
- `details`: a hash with more information about the error.

For example, if an environment does not exist for a given request, an error is raised similar to the following:

```json
{
  "kind" : "puppetlabs.orchestrator/unknown-environment",
  "msg" : "Unknown environment doesnotexist",
  "details" : {
    "environment" : "doesnotexist"
  }
}
```