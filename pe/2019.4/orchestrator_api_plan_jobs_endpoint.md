---
author: Kate Lopresti <kate.lopresti@puppet.com\>
---

# Puppet orchestrator API: plan jobs endpoint

Use the `/plan_jobs` endpoint to view details about plan jobs you have run.

**Parent topic:**[Puppet orchestrator API v1 endpoints](orchestrator_api_v1_endpoints.md)

## GET /plan\_jobs

List the known plan jobs sorted by name and in descending order.

### Parameters

The request accepts the following query parameters:

|Parameter|Definition|
|---------|----------|
|`limit`|Return only the most recent *n* number of jobs.|
|`offset`|Return results offset *n* records into the result set.|

### Response format

The response is a JSON object that contains a list of the known plan jobs, and information about the pagination.

|Key|Definition|
|---|----------|
|`items`|An array of all the plan jobs.|
|`id`|An absolute URL to the given plan job.|
|`name`|The ID of the plan job.|
|`state`|The current state of the plan job: `running`,`success`, or `failure`|
|`options`|Information about the plan job: `description`, `plan_name`, and any `parameters`.|
|`description`|The user-provided description for the plan job.|
|`plan_name`|The name of the plan that was run, for example `package::install`.|
|`parameters`|The parameters passed to the plan for the job.|
|`result`|The output from the plan job.|
|`owner`|The subject ID and login for the user that requested the job.|
|`created_timestamp`|The time the plan job was created.|
|`finished_timestamp`|The time the plan job finished.|
|`events`|A link to the events for a given plan job.|
|`status`|A hash of jobs that ran as part of the plan job, with associated lists of states and their enter and exit times.|
|`pagination`|Contains the information about the limit, offset and total number of items.|
|`limit`|The number of items the request was limited to.|
|`offset`|The offset from the start of the collection \(zero based\).|
|`total`|The total number of items in the collection, ignoring limit and offset.|

For example:

```json
{
  "items" : [ {
    "finished_timestamp" : null,
    "name" : "37",
    "events" : {
      "id" : "https://localhost:50310/orchestrator/v1/plan_jobs/37/events"
    },
    "state" : "running",
    "id" : "https://localhost:50310/orchestrator/v1/plan_jobs/37",
    "created_timestamp" : "YYYY-MM-DDT20:22:08Z",
    "options" : {
      "description" : "Testing myplan",
      "plan_name" : "myplan",
      "parameters" : {
        "nodes" : [ "localhost" ]
      }
    },
    "owner" : {
      "email" : "",
      "is_revoked" : false,
      "last_login" : "YYYY-MM-DDT20:22:06.327Z",
      "is_remote" : false,
      "login" : "admin",
      "is_superuser" : true,
      "id" : "42bf351c-f9ec-40af-84ad-e976fec7f4bd",
      "role_ids" : [ 1 ],
      "display_name" : "Administrator",
      "is_group" : false
    },
    "result" : null
  }, {
    "finished_timestamp" : null,
    "name" : "36",
    "events" : {
      "id" : "https://localhost:50310/orchestrator/v1/plan_jobs/36/events"
    },
    "state" : "running",
    "id" : "https://localhost:50310/orchestrator/v1/plan_jobs/36",
    "created_timestamp" : "YYYY-MM-DDT20:22:08Z",
    "options" : {
      "description" : "Testing myplan",
      "plan_name" : "myplan",
      "parameters" : {
        "nodes" : [ "localhost" ]
      }
    },
    "owner" : {
      "email" : "",
      "is_revoked" : false,
      "last_login" : "YYYY-MM-DDT20:22:06.327Z",
      "is_remote" : false,
      "login" : "admin",
      "is_superuser" : true,
      "id" : "42bf351c-f9ec-40af-84ad-e976fec7f4bd",
      "role_ids" : [ 1 ],
      "display_name" : "Administrator",
      "is_group" : false
    },
    "result" : null
  }, {
    "finished_timestamp" : null,
    "name" : "35",
    "events" : {
      "id" : "https://localhost:50310/orchestrator/v1/plan_jobs/35/events"
    },
    "state" : "running",
    "id" : "https://localhost:50310/orchestrator/v1/plan_jobs/35",
    "created_timestamp" : "YYYY-MM-DDT20:22:07Z",
    "options" : {
      "description" : "Testing myplan",
      "plan_name" : "myplan",
      "parameters" : {
        "nodes" : [ "localhost" ]
      }
    },
    "owner" : {
      "email" : "",
      "is_revoked" : false,
      "last_login" : "YYYY-MM-DDT20:22:06.327Z",
      "is_remote" : false,
      "login" : "admin",
      "is_superuser" : true,
      "id" : "42bf351c-f9ec-40af-84ad-e976fec7f4bd",
      "role_ids" : [ 1 ],
      "display_name" : "Administrator",
      "is_group" : false
    },
    "result" : null
  }, {
    "finished_timestamp" : null,
    "name" : "34",
    "events" : {
      "id" : "https://localhost:50310/orchestrator/v1/plan_jobs/34/events"
    },
    "state" : "running",
    "id" : "https://localhost:50310/orchestrator/v1/plan_jobs/34",
    "created_timestamp" : "YYYY-MM-DDT20:22:07Z",
    "options" : {
      "description" : "Testing myplan",
      "plan_name" : "myplan",
      "parameters" : {
        "nodes" : [ "localhost" ]
      }
    },
    "owner" : {
      "email" : "",
      "is_revoked" : false,
      "last_login" : "YYYY-MM-DDT20:22:06.327Z",
      "is_remote" : false,
      "login" : "admin",
      "is_superuser" : true,
      "id" : "42bf351c-f9ec-40af-84ad-e976fec7f4bd",
      "role_ids" : [ 1 ],
      "display_name" : "Administrator",
      "is_group" : false
    },
    "result" : null
  }, {
    "finished_timestamp" : null,
    "name" : "33",
    "events" : {
      "id" : "https://localhost:50310/orchestrator/v1/plan_jobs/33/events"
    },
    "state" : "running",
    "id" : "https://localhost:50310/orchestrator/v1/plan_jobs/33",
    "created_timestamp" : "YYYY-MM-DDT20:22:07Z",
    "options" : {
      "description" : "Testing myplan",
      "plan_name" : "myplan",
      "parameters" : {
        "nodes" : [ "localhost" ]
      }
    },
    "owner" : {
      "email" : "",
      "is_revoked" : false,
      "last_login" : "YYYY-MM-DDT20:22:06.327Z",
      "is_remote" : false,
      "login" : "admin",
      "is_superuser" : true,
      "id" : "42bf351c-f9ec-40af-84ad-e976fec7f4bd",
      "role_ids" : [ 1 ],
      "display_name" : "Administrator",
      "is_group" : false
    },
    "result" : null
  } ],
  "pagination" : {
    "limit" : 5,
    "offset" : 3,
    "total" : 40
  }
}
```

## GET /plan\_jobs/:job-id

List all the details of a given plan job.

### Response format

The response is a JSON object that lists all details of a given plan job. The following keys are used:

|Key|Defintion|
|---|---------|
|`id`|An absolute URL to the given plan job.|
|`name`|The ID of the plan job.|
|`state`|The current state of the plan job: `running`,`success`, or `failure`|
|`options`|Information about the plan job: `description`, `plan_name`, and any `parameters`.|
|`description`|The user-provided description for the plan job.|
|`plan_name`|The name of the plan that was run, for example `package::install`.|
|`parameters`|The parameters passed to the plan for the job.|
|`result`|The output from the plan job.|
|`owner`|The subject ID and login for the user that requested the job.|
|`timestamp`|The time when the plan job state last changed.|
|`created_timestamp`|The time the plan job was created.|
|`finished_timestamp`|The time the plan job finished.|
|`events`|A link to the events for a given plan job.|
|`status`|A hash of jobs that ran as part of the plan job, with associated lists of states and their enter and exit times.|

For example:

```json
{
  "id": "https://orchestrator.example.com:8143/orchestrator/v1/plan_jobs/1234",
  "name": "1234",
  "state": "success",
  "options": {
    "description": "This is a plan run",
    "plan_name": "package::install",
    "parameters": {
      "foo": "bar"
    }
  },
  "result": {
    "output": "test\n"
  },
  "owner": {
    "email": "",
    "is_revoked": false,
    "last_login": "YYYY-MM-DDT17:06:48.170Z",
    "is_remote": false,
    "login": "admin",
    "is_superuser": true,
    "id": "42bf351c-f9ec-40af-84ad-e976fec7f4bd",
    "role_ids": [
      1
    ],
    "display_name": "Administrator",
    "is_group": false
  },
  "timestamp": "YYYY-MM-DDT16:45:31Z",
  "status": {
    "1": [
      {
        "state": "running",
        "enter_time": "YYYY-MM-DDT18:44:31Z",
        "exit_time": "YYYY-MM-DDT18:45:31Z"
      },
      {
        "state": "finished",
        "enter_time": "YYYY-MM-DDT18:45:31Z",
        "exit_time": null
      }
    ],
    "2": [
      {
        "state": "running",
        "enter_time": "YYYY-MM-DDT18:44:31Z",
        "exit_time": "YYYY-MM-DDT18:45:31Z"
      },
      {
        "state": "failed",
        "enter_time": "YYYY-MM-DDT18:45:31Z",
        "exit_time": null
      }
    ]
  },
  "events": {
    "id": "https://localhost:8143/orchestrator/v1/plan_jobs/1234/events"
  }
}
```

### Error responses

For this endpoint, the `kind` key of the error displays the conflict.

-   `puppetlabs.orchestrator/validation-error`: if the `job-id` in the request is not an integer, the server returns a `400` response.
-   `puppetlabs.orchestrator/unknown-job`: if the plan job does not exist, the server returns a `404` response.

**Related information**  


[Puppet orchestrator API: error responses](orchestrator_api_error_responses.md)

