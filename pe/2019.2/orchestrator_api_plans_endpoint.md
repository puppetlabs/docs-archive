---
author: Logan Mantyla <logan.mantyla@puppet.com\>
---

# Puppet orchestrator API: plans endpoint

Use the `/plans` endpoint to see all known plans in your environments.

## GET /plans

List all known plans in a given environment.

### Parameters

The request accepts the following query parameters:

|Parameter|Definition|
|---------|----------|
|`environment`|Return the plans in a particular environment. Defaults to production.|

### Response format

The response is a JSON object that lists the known plans and where to find more information about them. It uses the following keys:

|Key|Definition|
|---|----------|
|`items`|Contains an array of all known plans for the specified environment.|
|`environment`|A map containing the `name` key with the environment name and a `code_id` key indicating the code id the plans are listed from. **Note:** `code_id` is always `null`.

|

Each item above has the following keys:

|Key|Definition|
|---|----------|
|`id`|An absolute URL to retrieve plan details.|
|`name`|The full name of the plan.|
|`permitted`|A boolean indicating if the user making the request is permitted to use the plan.|

For example:

```
{
  "environment": {
    "name": "production",
    "code_id": null
  },
  "items": [
    {
      "id": "https://orchestrator.example.com:8143/orchestrator/v1/plans/profile/firewall",
      "name": "profile::firewall",
      "permitted": true
    },
    {
      "id": "https://orchestrator.example.com:8143/orchestrator/v1/plans/profile/rolling_update",
      "name": "profile::rolling_update",
      "permitted": true
    },
    {
      "id": "https://orchestrator.example.com:8143/orchestrator/v1/plans/canary/random",
      "name": "canary::random",
      "permitted": false
    }
  ]
}
```

### Error responses

For this endpoint, the `kind` key of the error displays the conflict.

|Error|Definition|
|-----|----------|
|`puppetlabs.orchestrator/validation-error`|If the `environment` parameter is not a legal environment name, the server returns a `400` response.|
|`puppetlabs.orchestrator/unknown-environment`|If the specified `environment` doesn't exist, the server returns a `404` response.|

## GET /plans/:module/:planname

Return data about the specified plan, including metadata.

### Parameters

|Parameter|Definition|
|---------|----------|
|`environment`|Return the plan from a particular environment. Defaults to production. **Note:** `code_id` is always `null`.

|

### Response format

The response is a JSON object that includes information about the specified plan. The following keys are used:

|Key|Definition|
|---|----------|
|`id`|An absolute URL to retrieve plan details.|
|`name`|The full name of the plan.|
|`environment`|A map containing a `name` key with the environment name and a `code_id` key indicating the code id the plan is being listed from.|
|`metadata`|Currently always an empty object.|
|`permitted`|A boolean indicating if the user is permitted to use the plan or not.|

For example:

```
{
  "id": "https://orchestrator.example.com:8143/orchestrator/v1/plans/package/install",
  "name": "canary::random",
  "environment": {
    "name": "production",
    "code_id": null
  },
  "metadata": {},
  "permitted": true
}
```

### Error responses

|Error|Definition|
|-----|----------|
|`puppetlabs.orchestrator/validation-error`|If the `environment` parameter is not a legal environment name, or the module or plan name is invalid, the server returns a `400` response.|
|`puppetlabs.orchestrator/unknown-environment`|If the specified `environment` doesn't exist, the server returns a `404` response.|
|`puppetlabs.orchestrator/unknown-plan`|If the specified plan doesn't exist within that environment, the server returns a `404` response.|

