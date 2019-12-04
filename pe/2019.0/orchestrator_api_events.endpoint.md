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

