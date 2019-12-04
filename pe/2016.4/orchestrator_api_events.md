---
layout: default
title: "Puppet orchestrator API: events endpoint"
canonical: "/pe/latest/orchestrator_api_events.html"
---

## GET /jobs/:job-id/events

Retrieve all of the events that occurred during a given job.

### Parameters

The request accepts the following query parameter:

- `start`: start the list of events with the *nth* event. 

For example:

`https://orchestrator.example.com:8143/orchestrator/v1/jobs/352/events?start=1272`

### Response format

The response is a JSON object that details the events in a job. The following keys are used:

- `next-events`: a link to the next event in the job.
- `items`: a list of all events related to the job.
- `id`: the job ID.
- `type`: the current status of the event. See [event-types](#event-types).
- `timestamp`: the time when the event was created.
- `details`: information about the event.
- `message`: a message about the given event.

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

The response format for each event will contain one of the following event types, which is determined by the status of the event.

- `node_errored`: created when there was an error running Puppet on a node.
- `node_failed`: created when Puppet failed to run on a node.
- `node_finished`: created when puppet ran successfully on a node.
- `node_running`: created when Puppet starts running on a node.
- `node_skipped`: created when a Puppet run is skipped on a node (for example, if a dependency fails).
- `job_aborted`: created when a job is aborted without completing.

### Error responses

See the [error response documentation](./orchestrator_api_errors.html) for the general format of error responses. For this endpoint, the `kind` key of the error displays the conflict.

- `puppetlabs.orchestrator/validation-error`: if the `start` parameter or the `job-id` in the request are not integers, the server returns a `400` response.
- `puppetlabs.orchestrator/unknown-job`: if the job does not exist, the server returns a `404` response.