# Puppet orchestrator API: events endpoint

Use the `/events` endpoint to learn about events that occurred during an orchestrator job.

## GET /jobs/:job-id/events

Retrieve all of the events that occurred during a given job.

### Parameters

The request accepts this query parameter:

|Parameter|Definition|
|---------|----------|
|`start`|Start the list of events with the *nth* event.|

For example:

```
https://orchestrator.example.com:8143/orchestrator/v1/jobs/352/events?start=1272
```

### Response format

The response is a JSON object that details the events in a job, and uses these keys:

|Key|Definition|
|---|----------|
|`next-events`|A link to the next event in the job.|
|`items`|A list of all events related to the job.|
|`id`|The job ID.|
|`type`|The current status of the event. See event-types.|
|`timestamp`|The time when the event was created.|
|`details`|Information about the event.|
|`message`|A message about the given event.|

For example:

```json
{
  "next-events" : {
    "id" : "https://orchestrator.example.com:8143/orchestrator/v1/jobs/352/events?start=1272"
  },
  "items" : [ {
    "id" : "1267",
    "type" : "node_running",
    "timestamp" : "2016-05-05T19:50:08Z",
    "details" : {
      "node" : "puppet-agent.example.com",
      "detail" : {
        "noop" : false
      }
    },
    "message" : "Started puppet run on puppet-agent.example.com ..."
  }]
}
```

### Event types

The response format for each event contains one of these event types, which is determined by the status of the event.

|Event type|Definition|
|----------|----------|
|`node_errored`|Created when there was an error running Puppet on a node.|
|`node_failed`|Created when Puppet failed to run on a node.|
|`node_finished`|Created when puppet ran successfully on a node.|
|`node_running`|Created when Puppet starts running on a node.|
|`node_skipped`|Created when a Puppet run is skipped on a node \(for example, if a dependency fails\).|
|`job_aborted`|Created when a job is aborted without completing.|

### Error responses

For this endpoint, the `kind` key of the error displays the conflict.

|Key|Definition|
|---|----------|
|`puppetlabs.orchestrator/validation-error`|If the `start` parameter or the `job-id` in the request are not integers, the server returns a `400` response.|
|`puppetlabs.orchestrator/unknown-job`|If the job does not exist, the server returns a `404` response.|

**Related information**  


[Puppet orchestrator API: error responses](orchestrator_api_error_responses.md)

## GET /plan\_jobs/:job-id/events

Retrieve all of the events that occurred during a given plan job.

### Parameters

The request accepts the following query parameter:

-   `start`: The lowest database ID of an event that should be shown.

For example:

https://orchestrator.example.com:8143/orchestrator/v1/plan\_jobs/352/events?start=1272

### Response format

The response is a JSON object that derails the events in a plan job. he following keys are used:

-   `next-events`: a link to the next event in the job.
    -   `id`: the url of the next event.
    -   `event`: the next event id.
-   `items`: a list of all events related to the job.
    -   `id`: the id of the event.
    -   `type`: the type of event. See `event-types`.
    -   `timestamp`: the time when the event was created.
    -   `details`: details of the event, differs based on the type of the event. See the potential values section for more detailed information.

For example:

```
{
  "next-events" : {
    "id" : "https://orchestrator.example.com:8143/orchestrator/v1/plan_jobs/352/events?start=1272",
    "event": "1272"
  },
  "items" : [ {
    "id" : "1267",
    "type" : "task_start",
    "timestamp" : "2016-05-05T19:50:08Z",
    "details" : {
      "job-id" : "8765"
    }
  },
  {
    "id" : "1268",
    "type" : "plan_finished",
    "timestamp" : "2016-05-05T19:50:14Z",
    "details" : {
      "plan-id" : "1234",
      "result" : {
        "Plan output"
      },
    },
  }]
}
```

### Event types

The response format for each event will contain one of the following event types, which is determined by the status of the event.

-   `task_start`: created when a task run is started
-   `script_start`: created when a script run as part of a plan is started
-   `command_start`: created when a command run as part of a plan is started
-   `upload_start`: created when a file upload as part of a plan is started
-   `wait_start`: created when a `wait_until_available()` call as part of a plan is started
-   `out_message`: created when `out::message` is called as part of a plan. `details` will contain a `message` key with a value containing the message sent with `out::message` truncated to 1,024 bytes. The full message content can be obtained using the [GET /plan\_jobs/:job-id/event/:event-id](orchestrator_api_events.endpoint.md#) endpoint.
-   `apply_start`: created when a puppet apply run as part of a plan is started
-   `plan_finished`: created when a plan job successfully finishes
-   `plan_failed`: created when a plan job fails

### "Sub" plans

If a plan contains the `run_plan()` function it will begin execution of another "sub" plan during the execution of the plan job. "Sub" plans will not receive their own plan job, they will execute as part of the original plan job. There are specific events for "sub" plans that indicate a plan within a plan has started or finished:

-   `plan_start`: created when a new plan was kicked off from the current plan job using the `run_plan()` function
-   `plan_end`: created when a plan started using `run_plan()` has finished

    **Note:** `plan_end` indicates the end of a "sub" plan \(i.e. the plan job has not finished yet, but the "sub" plan has\), while `plan_finished` indicates the end of a plan job.


### Potential values in `details`

**task and plan actions**

Includes the job-id if the event is a plan action or task \(i.e. `task_start`, `command_start`, `apply_start`, etc\).

```
{
    "type" : "task_start",
    "timestamp" : "2019-09-30T22:22:32Z",
    "details" : {
      "job-id" : 69
    },
    "id" : "80"
  }
```

`plan_finished` **or** `plan_failed`

If the event type is `plan_finished` or `plan_failed`, details will include `plan-id` and `result`.

```
{
  "type" : "plan_finished",
  "timestamp" : "2019-09-30T22:22:33Z",
  "details" : {
    "result" : "plan result",
    "plan-id" : "9"
  },
  "id" : "81"
}
```

`out_message`

If the event type is `out_message`, `details` will include `message`.

```
{
  "type" : "out_message",
  "timestamp" : "2019-09-30T22:22:32Z",
  "details" : {
    "message" : "message sent from a plan"
  }
}
```

`plan_start` or `plan_end`

If the event type is `plan_start` or `plan_end`, `details` will include the plan that ran \(`plan`\) and `plan_end` will include `duration`.

`plan_start`:

```
{
  "type" : "plan_start",
  "timestamp" : "2019-09-30T22:22:31Z",
  "details" : {
    "plan" : "test::sub_plan"
  },
  "id" : "76"
}
```

`plan_end`:

```
{
  "type" : "plan_end",
  "timestamp" : "2019-09-30T22:22:32Z",
  "details" : {
    "plan" : "test::sub_plan",
    "duration" : 0.647551
  }
}
```

### Error responses

See the [error response documentation](orchestrator_api_error_responses.md) for the general format of error responses. For the endpoint, the kind key of the error displays the conflict.

-   `puppetlabs.orchestrator/validation-error`: if the `start` parameter or the `job-id` in the request are not integers, the server returns a `400` response.
-   `puppetlabs.orchestrator/unknown-job`: if the plan job does not exist, the server returns a `404` response.

## GET /plan\_jobs/:job-id/event/:event-id

Fetch a specific event for a job.

### Response format

The response is a JSON object of the event.

-   `id`: ID of this event
-   `type`: event type
-   `timestamp`: when the event occured
-   `details`: hash of detail fields specific to the event type

**Note:** Unlike the `/plan_jobs/:job-id/events` endpoint, no truncation of fields occurs.

For example:

```
{    
    "id": "1265",
    "type": "out_message",
    "timestamp": "2016-05-05T19:50:06Z",
    "details": {
        "message": "this is an output message"
    }
}
```

### Error responses

See the [error response documentation](orchestrator_api_error_responses.md) for the general format of error responses. For this endpoint, the kind key of the error displays the conflict.

-   `puppetlabs.orchestrator/validation-error`: if the `job-id` in the request is not an integer, the server returns a `400` response.
-   `puppetlabs.orchestrator/unknown-job`: if the plan job does not exist, the server returns a `404` response.
-   `puppetlabs.orchestrator/mismatched-job-event-id`: if the `event-id` specified does not belong to the `job-id` specified, the server returns a `404` response.

