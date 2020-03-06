# Puppet orchestrator API: jobs endpoint

Use the `/jobs` endpoint to examine orchestrator jobs and their details.

## GET /jobs

List all of the jobs known to the orchestrator.

### Parameters

The request accepts the following query parameters:

|Parameter|Definition|
|---------|----------|
|`limit`|Return only the most recent *n* number of jobs.|
|`offset`

|Return results offset *n* records into the result set.|
|`order_by`|Return results ordered by a column. Ordering is available on `owner`, `timestamp`, `environment`, `name`, and `state`. Orderings requested on `owner` are applied to the `login` subfield of `owner`.|
|`order`|Indicates whether results should be returned in ascending or descending order. Valid values are "asc" and "desc". Defaults to "asc"|

For example:

```
https://orchestrator.example.com:8143/orchestrator/v1/jobs/352/jobs?limit=20&offset=20
```

### Response format

The response is a JSON object that lists orchestrator jobs and associated details, and uses these keys:

|Key|Definition|
|---|----------|
|`items`|Contains an array of all the known jobs.|
|`id`|An absolute URL to the given job.|
|`name`|The name of the given job.|
|`state`|The current state of the job: ```new```, `ready`, `running`, `stopping`, `stopped`, `finished`, or `failed`.|
|`command`|The command that created that job.|
|`options`|All of the options used to create that job. The schema of options might vary based on the command.|
|`owner`|The subject id and login for the user that requested the job.|
|`description`|\(deprecated\) A user-provided description of the job. For future compatibility the description in options should be used.|
|`timestamp`|The time when the job state last changed.|
|`environment`|\(deprecated\) The environment that the job operates in.|
|`node_count`|The number of nodes the job runs on.|
|`node_states`|Aa JSON map containing the counts of nodes involved with the job by current node state. Unrepresentend states are not displayed. This field is `null` when no nodes exist for a job.

|
|`nodes`|A link to get more information about the nodes participating in a given job.|
|`report`|A link to the report for a given job.|
|`events`|A link to the events for a given job.|

For example:

```json
{
  "items" : [
    {
      "id" : "https://orchestrator.example.com:8143/orchestrator/v1/jobs/1234",
      "name": "1234",
      "state" : "finished",
      "command" : "deploy",
      "node_count" : 5,
      "node_states" : {
        "finished": 2,
        "errored": 1,
        "failed": 1,
        "running": 1
      },
      "options" : {
        "concurrency" : null,
        "noop" : false,
        "trace" : false,
        "debug" : false,
        "scope" : { },
        "enforce_environment" : true,
        "environment" : "production",
        "evaltrace" : false,
        "target" : null,
        "description" : "deploy the web app",
      },
      "owner" : {
        "id" : "751a8f7e-b53a-4ccd-9f4f-e93db6aa38ec",
        "login" : "brian"
      },
      "description" : "deploy the web app",
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
  ],
  "pagination": {
    "limit": 20,
    "offset": 0,
    "total": 42
  }
}
...
```

### Error responses

For this endpoint, the `kind` key of the error displays the conflict.

|Key|Definition|
|---|----------|
|`puppetlabs.orchestrator/validation-error`|If the `limit` parameter is not an integer, the server returns a `400` response.|

**Related information**  


[Puppet orchestrator API: error responses](orchestrator_api_error_responses.md)

## GET /jobs/:job-id

List all details of a given job.

### Response format

The response is a JSON object that lists all details of a given job, and uses these keys:

|Key|Definition|
|---|----------|
|`id`|An absolute URL to the given job.|
|`name`|The name of the given job.|
|`state`|The current state of the job: `new`, `ready`, `running`, `stopping`, `stopped`, `finished`, or `failed`.|
|`command`|The command that created that job.

|
|`options`|All of the options used to create that job.|
|`owner`|The subject id and login for the user that requested the job.|
|`description`|\(deprecated\) A user-provided description of the job.|
|`timestamp`|The time when the job state last changed.|
|`environment`|The environment that the job operates in.|
|`node_count`|The number of nodes the job runs on.|
|`nodes`|A link to get more information about the nodes participating in a given job.|
|`report`|A link to the report for a given job.|
|`events`|A link to the events for a given job.|
|`status`|The various enter and exit times for a given job state.|

For example:

```json
{
  "id" : "https://orchestrator.example.com:8143/orchestrator/v1/jobs/1234",
  "name" : "1234",
  "command" : "deploy",
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
    "login" : "admin"
  },
  "description" : "deploy the web app",
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

### Error responses

For this endpoint, the `kind` key of the error displays the conflict.

|Key|Definition|
|---|----------|
|`puppetlabs.orchestrator/validation-error`|If the `job-id` in the request is not an integer, the server returns a `400` response.|
|`puppetlabs.orchestrator/unknown-job`|If the job does not exist, the server returns a `404` response.|

**Related information**  


[Puppet orchestrator API: error responses](orchestrator_api_error_responses.md)

## GET /jobs/:job-id/nodes

List all of the nodes associated with a given job.

### Response format

The response is a JSON object that details the nodes associated with a job.

`next-events` is an object containing information about where to find events about the job. It has the following keys:

|Key|Definition|
|---|----------|
|`id`|The url of the next event.|
|`event`|The next event id.

|

`items`: a list of all the nodes associated with the job, where each node has the following keys:

|Key|Definition|
|---|----------|
|`timestamp`|\(deprecated\) The time of the most recent activity on the node. Prefer `start_timestamp` and `finish_timestamp`.|
|`start_timestamp`|The time the node starting running or `nil` if the node hasn't started running or was skipped.

|
|`finish_timestamp`|The time the node finished running or `nil` if the node hasn't finished running or was skipped.

|
|`duration`|The duration of the puppet run in seconds if the node has finished running, the duration in seconds that has passed since the node started running if it is currently running, or `nil` if the node hasn't started running or was skipped|
|`state`|The current state of the job.|
|`transaction_uuid`|\(deprecated\) The ID used to identify the nodes last report.|
|`name`|The hostname of the node.|
|`details`|information about the last event and state of a given node.|
|`result`|The result of a successful, failed, errored node-run. The schema of this varies.|

For example:

```json
{
  "next-events": {
    "id": "https://orchestrator.example.com:8143/orchestrator/v1/jobs/3/events?start=10",
    "event": "10"
  },
  "items" : [ {
    "timestamp" : "2015-07-13T20:37:01Z",
    "start_timestamp" : "2015-07-13T20:36:13Z",
    "finish_timestamp" : "2015-07-13T20:37:01Z",
    "duration" : 48.0,
    "state" : "state",
    "transaction_uuid" : :uuid,
    "name" : "wss6c3w9wngpycg.example.com",
    "details" : {
      "message": "Message of latest event"
    },
    "result": {
      "output_1": "success",
      "output_2": [1, 1, 2, 3,]
    }
  },{
  ...
  } ]
}
```

### Results

The result field is available after a node finishes, fails, or errors and contains the contents of the details of the corresponding event. In task jobs this is the result of executing the task. For puppet jobs it should contain metrics from the puppet run.

For example:

An error when running a task:

```
"result" : {
  "msg" : "Running tasks is not supported for agents older than version 5.1.0",
  "kind" : "puppetlabs.orchestrator/execution-failure",
  "details" : {
    "node" : "copper-6"
   }
}
```

Raw stdout from a task:

```
"result" : {
  "output" : "test\n"
}

```

Structured output from a task

```
"result" : {
  "status" : "up to date",
  "version" : "5.0.0.201.g879fc5a-1.el7"
}
```

Error output from a task

```
"result" : {
  "error" : "Invalid task name 'package::status'"
}

```

Puppet run results:

```
"result" : {
  "hash" : "d7ec44e176bb4b2e8a816157ebbae23b065b68cc",
  "noop" : {
    "noop" : false,
    "no_noop" : false
  },
  "status" : "unchanged",
  "metrics" : {
    "corrective_change" : 0,
    "out_of_sync" : 0,
    "restarted" : 0,
    "skipped" : 0,
    "total" : 347,
    "changed" : 0,
    "scheduled" : 0,
    "failed_to_restart" : 0,
    "failed" : 0
  },
  "environment" : "production",
  "configuration_version" : "1502024081"
}
```

### Details

The details field contains information based on the last event and current state of the node and might be empty. In some cases it might duplicate data from the results key for historical reasons.

If the node state is `finished` or `failed` the details hash can include a message and a report-url. \(deprecated\) for jobs started with the run command it also duplicates some information from the result.

```json
{
  "items" : [ {
    "timestamp" : "2015-07-13T20:37:01Z",
    "state" : "finished",
    "transaction_uuid" : :uuid,
    "name" : "wss6c3w9wngpycg.example.com",
    "details" : {
      "report-url" : "https://peconsole.example.com/#/cm/report/a15bf509dd7c40705e4e1c24d0935e2e8a1591df",
      "message": "Finished puppet run on wss6c3w9wngpycg.example.com - Success!"
    }
    "result" : {
      "metrics" : {
        "total" : 82,
        "failed" : 0,
        "changed" : 51,
        "skipped" : 0,
        "restarted" : 2,
        "scheduled" : 0,
        "out_of_sync" : 51,
        "failed_to_restart" : 0
      },
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

If the node state is `running`, the service returns the `run-time` \(in seconds\).

```json
{
  "items" : [ {
    "timestamp" : "2015-07-13T20:37:01Z",
    "state" : "running",
    "transaction_uuid" : :uuid,
    "name" : "wss6c3w9wngpycg.example.com",
    "details" : {
      "run-time": 30,
      "message": "Started puppet run on wss6c3w9wngpycg.example.com..."
    }
  },{
  ...
  } ]
}
```

### Error responses

For this endpoint, the `kind` key of the error displays the conflict.

|Key|Definition|
|---|----------|
|`puppetlabs.orchestrator/validation-error`|If the `job-id` in the request is not an integer, the server returns a `400` response.|
|`puppetlabs.orchestrator/unknown-job`|If the job does not exist, the server returns a `404` response.|

**Related information**  


[Puppet orchestrator API: error responses](orchestrator_api_error_responses.md)

## GET /jobs/:job-id/report

Returns a report for a given job.

### Response format

The response is a JSON object that reports the status of a job, and uses these keys:

|Key|Definition|
|---|----------|
|`items`|An array of all the reports associated with a given job.|
|`node`|The hostname of a node.|
|`state`|The current state of the job.|
|`timestamp`|The time when the job was created.|
|`events`|Any events associated with that node during the job.|

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

### Error responses

For this endpoint, the `kind` key of the error displays the conflict.

|Key|Definition|
|---|----------|
|`puppetlabs.orchestrator/validation-error`|If the `job-id` in the request is not an integer, the server returns a `400` response.|
|h`puppetlabs.orchestrator/unknown-job`|If the job does not exist, the server returns a `404` response.|

**Related information**  


[Puppet orchestrator API: error responses](orchestrator_api_error_responses.md)

