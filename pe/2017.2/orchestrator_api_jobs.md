---
layout: default
title: "Puppet orchestrator API: jobs endpoint"
canonical: "/pe/latest/orchestrator_api_jobs.html"
---

Use the /jobs endpoint to examine orchestrator jobs and their details.


## GET /jobs

List all of the jobs known to the orchestrator.

### Parameters

The request accepts this query parameter:

Parameter | Definition
----------|-----------
`limit` | Return only the most recent *n* number of jobs.

For example:

`https://orchestrator.example.com:8143/orchestrator/v1/jobs?limit=20`

### Response format

The response is a JSON object that lists orchestrator jobs and associated details, and uses these keys:

Key | Definition
----|-----------
`items` | Contains an array of all the known jobs.
`id` | An absolute URL to the given job.
`name` | The name of the given job.
`state` | The current state of the job.
`options` | All of the options used to create that job.
`owner`| The subject id and login for the user that requested the job.
`timestamp`| The time when the job state last changed.
`environment` | The environment that the job operates in.
`node_count` | The number of nodes the job will run on.
`nodes` | A link to get more information about the nodes participating in a given job.
`report`| A link to the report for a given job.
`events`| A link to the events for a given job.

For example:

```json
{
  "items" : [
    {
      "id" : "https://orchestrator.example.com:8143/orchestrator/v1/jobs/1234",
      "name": "1234",
      "state" : "finished",
      "node_count" : 5,
      "options" : {
        "concurrency" : null,
        "noop" : false,
        "trace" : false,
        "debug" : false,
        "scope" : { },
        "enforce_environment" : true,
        "environment" : "production",
        "evaltrace" : false,
        "target" : null
      },
      "owner" : {
        "id" : "751a8f7e-b53a-4ccd-9f4f-e93db6aa38ec",
        "login" : "brian"
      },
      "timestamp": "2016-05-20T16:45:31Z",
      "environment" : { "name" : "production" },
      "report" : {
      "id" : "https://localhost:8143/orchestrator/v1/jobs/375/report"
      },
      "events" : {
       "id" : "https://localhost:8143/orchestrator/v1/jobs/375/events"
      },
      "nodes" : {
        "id" : "https://localhost:8143/orchestrator/v1/jobs/375/nodes"
      }
    },
    ...
  ]
}
...
```

### Error Responses

See the [error response documentation](./orchestrator_api_errors.html) for the general format of error responses. For this endpoint, the `kind` key of the error displays the conflict.

Key | Definition
----|-----------
`puppetlabs.orchestrator/validation-error` | If the `limit` parameter is not an integer, the server returns a `400` response.

## GET /jobs/:job-id

List all details of a given job.

### Response Format

The response is a JSON object that lists all details of a given job, and uses these keys:

Key | Definition
----|-----------
`id` | An absolute URL to the given job.
`name` | The name of the given job.
`state`| the current state of the job.
`options`| All of the options used to create that job.
`owner`| The subject id and login for the user that requested the job.
`timestamp`| The time when the job state last changed.
`environment`| The environment that the job operates in.
`node_count`| The number of nodes the job will run on.
`nodes`| A link to get more information about the nodes participating in a given job.
`report`| A link to the report for a given job.
`events`| A link to the events for a given job.
`status`| The various enter and exit times for a given job state.

For example:

```json
{
  "id" : "https://orchestrator.example.com:8143/orchestrator/v1/jobs/1234",
  "name" : "1234",
  "options" : {
    "concurrency" : null,
    "noop" : false,
    "trace" : false,
    "debug" : false,
    "scope" : {
      "application" : "Wordpress_app" },
    "enforce_environment" : true,
    "environment" : "production",
    "evaltrace" : false,
    "target" : null
  },
  "node_count" : 5,
  "owner" : {
    "id" : "751a8f7e-b53a-4ccd-9f4f-e93db6aa38ec",
    "login" : "brian"
  },
  "timestamp": "2016-05-20T16:45:31Z",
  "environment" : {
    "name" : "production"
  },
  "status" : [ {
    "state" : "new",
    "enter_time" : "2016-04-11T18:44:31Z",
    "exit_time" : "2016-04-11T18:44:31Z"
  }, {
    "state" : "ready",
    "enter_time" : "2016-04-11T18:44:31Z",
    "exit_time" : "2016-04-11T18:44:31Z"
  }, {
    "state" : "running",
    "enter_time" : "2016-04-11T18:44:31Z",
    "exit_time" : "2016-04-11T18:45:31Z"
  }, {
    "state" : "finished",
    "enter_time" : "2016-04-11T18:45:31Z",
    "exit_time" : null
  } ],
  "nodes" : { "id" : "https://orchestrator.example.com:8143/orchestrator/v1/jobs/1234/nodes" },
  "report" : { "id" : "https://orchestrator.example.com:8143/orchestrator/v1/jobs/1234/report" }
}
```

### Error Responses

See the [error response documentation](./orchestrator_api_errors.html) for the general format of error responses. For this endpoint, the `kind` key of the error displays the conflict.

Key | Definition
----|-----------
`puppetlabs.orchestrator/validation-error` | If the `job-id` in the request is not an integer, the server returns a `400` response.
`puppetlabs.orchestrator/unknown-job` | If the job does not exist, the server returns a `404` response.

## GET /jobs/:job-id/nodes

List all of the nodes associated with a given job.

### Response Format

The response is a JSON object that details the nodes associated with a job, and uses these keys:

Key | Definition
----|-----------
`items` | A list of all the given nodes assicated with the job.
`timestamp` | The time when the job was created.
`state` | The current state of the job.
`transaction_uuid`| The ID used to identify the nodes last report.
`name` | The hostanme of the node.
`details` | A hash containing the last message for a given node.

For example:

```json
{
  "items" : [ {
    "timestamp" : "2015-07-13T20:37:01Z",
    "state" : "state",
    "transaction_uuid" : :uuid,
    "name" : "wss6c3w9wngpycg.example.com",
    "details" : {
      "message": "Message of latest event"
    }
  },{
  ...
  } ]
}
```

Depending on the state, the service returns a `details` hash that gives more information about what is happening with the nodes associated with the `:job-id`.

If the node state is `finished` or `failed`, the information returned includes some basic metrics as well as the console report URL.

```json
{
  "items" : [ {
    "timestamp" : "2015-07-13T20:37:01Z",
    "state" : "finished",
    "transaction_uuid" : :uuid,
    "name" : "wss6c3w9wngpycg.example.com",
    "details" : {
      "report-url" : "https://peconsole.example.com/#/cm/report/a15bf509dd7c40705e4e1c24d0935e2e8a1591df",
      "metrics" : {
        "total" : 82,
        "failed" : 0,
        "changed" : 51,
        "skipped" : 0,
        "restarted" : 2,
        "scheduled" : 0,
        "out_of_sync" : 51,
        "failed_to_restart" : 0
      }
    },
    "message": "Finished puppet run on wss6c3w9wngpycg.example.com - Success!"
  },{
  ...
  } ]
}
```

If the node state is `skipped` or `errored`, the service includes a `:detail` key that explains why a node is in that state.


```json
{
  "items" : [ {
    "timestamp" : "2015-07-13T20:37:01Z",
    "state" : "failed",
    "transaction_uuid" : :uuid,
    "name" : "wss6c3w9wngpycg.example.com",
    "details" : {
      "message": "Error running puppet on wss6c3w9wngpycg.example.com: java.net.Exception: Something went wrong"
    }
  },{
  ...
  } ]
}
```

If the node state is `running`, the service returns the `run-time` (in seconds).

```json
{
  "items" : [ {
    "timestamp" : "2015-07-13T20:37:01Z",
    "state" : "running",
    "transaction_uuid" : :uuid,
    "name" : "wss6c3w9wngpycg.example.com",
    "details" : {
      "run-time": 30
    },
    "message": "Started puppet run on wss6c3w9wngpycg.example.com..."
  },{
  ...
  } ]
}
```

### Error Responses

See the [error response documentation](./orchestrator_api_errors.html) for the general format of error responses. For this endpoint, the `kind` key of the error displays the conflict.

Key | Definition
----|-----------
`puppetlabs.orchestrator/validation-error`| If the `job-id` in the request is not an integer, the server returns a `400` response.
`puppetlabs.orchestrator/unknown-job`| If the job does not exist, the server returns a `404` response.

## GET /jobs/:job-id/report

Returns a report for a given job.

### Response Format

The response is a JSON object that reports the status of a job, and uses these keys:

Key | Definition
----|------------
`items` | An array of all the reports associated with a given job.
`node` | The hostname of a node.
`state` | The current state of the job.
`timestamp` | The time when the job was created.
`events` | Any events associated with that node during the job.

For example:

```json
{
  "items" : [ {
    "node" : "wss6c3w9wngpycg.example.com",
    "state" : "running",
    "timestamp" : "2015-07-13T20:37:01Z",
    "events" : [ ]
  }, {
  ...
  } ]
}
```

### Error Responses

See the [error response documentation](./orchestrator_api_errors.html) for the general format of error responses. For this endpoint, the `kind` key of the error displays the conflict.

Key | Definition
----|-----------
`puppetlabs.orchestrator/validation-error` | If the `job-id` in the request is not an integer, the server returns a `400` response.
h`puppetlabs.orchestrator/unknown-job` | If the job does not exist, the server returns a `404` response.
