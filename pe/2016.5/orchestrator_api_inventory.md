---
layout: default
title: "Puppet orchestrator API: inventory endpoint"
canonical: "/pe/latest/orchestrator_api_inventory.html"
---

## GET /inventory

List all nodes that are connected to the PCP broker.

### Response format

The response is a JSON object containing a list of records for connected nodes.

```json
{
  "items" : [
    {
      "name" : "foo.example.com",
      "connected" : true,
    },
    {
      "name" : "bar.example.com",
      "connected" : true,
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

The response is a JSON object indicating whether the queried node is connected, which broker it's connected to, and when it connected.

```json
{
  "name" : "foo.example.com",
  "connected" : true,
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

The response is a JSON object with a record for each node in the request, with a field indicating whether that node is connected, which broker it's connected to, and when it connected.

```json
{
  "items" : [
    {
      "name" : "foo.example.com",
      "connected" : true,
    },
    {
      "name" : "bar.example.com",
      "connected" : false
    },
    {
      "name" : "baz.example.com",
      "connected" : true,
    }
  ]
}
```

### Error Responses

See the [error response documentation](./orchestrator_api_errors.html) for the general format of error responses. For this endpoint, the `kind` key of the error displays the conflict.

The server returns a 500 response if the PCP broker can't be reached.
