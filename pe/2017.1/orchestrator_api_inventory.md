---
layout: default
title: "Puppet orchestrator API: inventory endpoint"
canonical: "/pe/latest/orchestrator_api_inventory.html"
---

## GET /inventory

List all nodes that are connected to the PCP broker.

### Response format

The response is a JSON object containing a list of records for connected nodes, using these keys:

- `name`: the name of the connected node.
- `connected`: the connection status is either `true` or `false`.
- `broker` : the PCP broker the node is connected to.
- `timestamp`: The time when the connection was made.

```json
{
  "items" : [
    {
      "name" : "foo.example.com",
      "connected" : true,
      "broker" : "pcp://broker1.example.com/server",
      "timestamp": "2016-010-22T13:36:41.449Z"
    },
    {
      "name" : "bar.example.com",
      "connected" : true,
      "broker" : "pcp://broker2.example.com/server",
      "timestamp" : "2016-010-22T13:39:16.377Z"
    }
  ]
}
```

### Error Responses

See the [error response documentation](./orchestrator_api_errors.html) for the general format of error responses. For this endpoint, the `kind` key of the error displays the conflict.

The server returns a 500 response if the PCP broker can't be reached.

## GET /inventory/:node

Return information about whether the requested node is connected to the PCP broker.

### Response format

The response is a JSON object indicating whether the queried node is connected, using these keys:

- `name`: the name of the connected node.
- `connected`: the connection status is either `true` or `false`.
- `broker` : the PCP broker the node is connected to.
- `timestamp`: the time when the connection was made.

```json
{
  "name" : "foo.example.com",
  "connected" : true,
  "broker" : "pcp://broker.example.com/server",
  "timestamp" : "2017-03-29T21:48:09.633Z"
}
```

### Error Responses

See the [error response documentation](./orchestrator_api_errors.html) for the general format of error responses. For this endpoint, the `kind` key of the error displays the conflict.

The server returns a 500 response if the PCP broker can't be reached.

## POST /inventory

Check if the given list of nodes is connected to the PCP broker.

### Request format

The request body is a JSON object specifying a list of nodes to check.

```json
{
  "nodes" : [
    "foo.example.com",
    "bar.example.com",
    "baz.example.com"
  ]
}
```

### Response format

The response is a JSON object with a record for each node in the request, using these keys:

- `name`: the name of the connected node.
- `connected`: the connection status is either `true` or `false`.
- `broker` : the PCP broker the node is connected to.
- `timestamp`: the time when the connection was made.

```json
{
  "items" : [
    {
      "name" : "foo.example.com",
      "connected" : true,
      "broker" : "pcp://broker.example.com/server",
      "timestamp" : "2017-07-14T15:57:33.640Z"
    },
    {
      "name" : "bar.example.com",
      "connected" : false
    },
    {
      "name" : "baz.example.com",
      "connected" : true,
      "broker" : "pcp://broker.example.com/server",
      "timestamp" : "2017-07-14T15:41:19.242Z"
    }
  ]
}
```

### Error Responses

See the [error response documentation](./orchestrator_api_errors.html) for the general format of error responses. For this endpoint, the `kind` key of the error displays the conflict.

The server returns a 500 response if the PCP broker can't be reached.
