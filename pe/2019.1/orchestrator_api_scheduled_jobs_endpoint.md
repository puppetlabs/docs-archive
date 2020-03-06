---
author: Kate Lopresti <kate.lopresti@puppet.com\>
---

# Puppet orchestrator API: scheduled jobs endpoint

Use the `/scheduled_jobs` endpoint to gather information about orchestrator jobs scheduled to run.

## GET /scheduled\_jobs

List scheduled jobs in ascending order.

### Parameters

The request accepts the following query parameters:

|Parameter|Definition|
|---------|----------|
|`limit`|Return only the most recent *n* number of jobs.|
|`offset`

|Return results offset *n* records into the result set.|

For example:

```
https://orchestrator.example.com:8143/orchestrator/v1/scheduled_jobs?limit=20&offset=20
```

### Response format

The response is a JSON object that contains a list of the known jobs and information about the pagination.

|Key|Definition|
|---|----------|
|`items`|Contains an array of all the scheduled jobs.|
|`id`|An absolute URL to the given job.|
|`name`|The ID of the scheduled job|
|`type`|The type of scheduled job \(currently only `task`\)|
|`task`|The name of the task associated with the scheduled job |
|`scope`|The specification of the targets for the task.|
|`environment`|The environment that the job operates in.|
|`owner`|The specification for the user that requested the job.|
|`description`|A user-provided description of the job.|
|`scheduled_time`|An ISO8601 specification of when the scheduled job runs.|
|`noop`|True if the job should run in no-operation mode, false otherwise|
|`pagination`|Contains the information about the limit, offset and total number of items|
|`limit`|A restricted number of items for the request to return|
|`offset`|A number offset from the start of the collection \(zero based\)|
|`total`|The total number of items in the collection, ignoring `limit` and `offset`|

For example:

```json
{
    "items": [
        {
            "id": "https://orchestrator.example.com:8143/orchestrator/v1/scheduled_jobs/10",
            "name": "10",
            "type": "task",
            "scope": {"nodes": ["foo.example.com", "bar.example.com"]},
            "enviroment": "production",
            "owner": {
                "id": "751a8f7e-b53a-4ccd-9f4f-e93db6aa38ec",
                "login": "fred"
             },
             "description": "front face the nebaclouser",
             "scheduled_time": "2027-05-05T19:50:08.000Z",
             "noop": false,
         },
         {
            "id": "https://orchestrator.example.com:8143/orchestrator/v1/scheduled_jobs/9",
            "name": "9",
            "type": "task",
            "scope": {"nodes": ["east.example.com", "west.example.com"]},
            "enviroment": "production",
            "owner": {
                "id": "751a8f7e-b53a-4ccd-9f4f-e93db6aa38ec",
                "login": "fred"
             },
             "description": "rear face the cranfitouser",
             "scheduled_time": "2027-05-05T19:55:08.000Z",
             "noop": false,
         }
      ],
      "pagination": {
        "limit": 2,
        "offset": 5,
        "total: 15
       }
 }
```

### Error responses

For this endpoint, the `kind` key of the error displays the conflict.

|Key|Definition|
|---|----------|
|`puppetlabs.orchestrator/validation-error`|If the `limit` or `offset` parameter is not an integer, the server returns a `400` response.|

**Related information**  


[Puppet orchestrator API: error responses](orchestrator_api_error_responses.md)

## DELETE /scheduled\_jobs/:job-id

Delete a scheduled job.

-   Response 204

-   Response 403

    -   Body

```
{
  "kind": "puppetlabs.orchestrator/not-permitted",
  "msg": "Not authorized to delete job {id}"
}
```


