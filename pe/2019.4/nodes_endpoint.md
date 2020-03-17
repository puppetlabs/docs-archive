# Nodes check-in history endpoint

Use the `nodes` endpoint to retrieve historical information about nodes that have checked into the node classifier.

## Enable check-in storage to use this endpoint

Node check-in storage is disabled by default because it can place excessive loads on larger deployments. You must enable node check-in storage before using the check-in history endpoint. If node check-in storage is not enabled, the endpoint returns an empty array.

To enable node check-in storage, set the `classifier_node_check_in_storage` parameter in the `puppet_enterprise::profile::console` class to `true`.

**Parent topic:**[Node classifier API v1](node_classifier_service_api.md)

**Related information**  


[Set configuration data](grouping_and_classifying_nodes.md#)

## GET /v1/nodes

Use the `/v1/nodes` endpoint to retrieve a list of all nodes that have checked in with the node classifier, each with their check-in history.

### Query Parameters

-   **limit**

    Controls the maximum number of nodes returned. `limit=10` returns 10 nodes.

-   **offset**

    Specifies how many nodes to skip before the first returned node. `offset=20` skips the first 20 nodes.


CAUTION:

In deployments with large numbers of nodes, a large or unspecified `limit` might cause the console-services process to run out of memory and crash.

### Response format

The response is a JSON array of node objects. Each node object contains these two keys:

|Key|Definition|
|---|----------|
|`name`|The name of the node according to Puppet \(a string\).|
|`check_ins`|An array of check-in objects \(described below\).|

Each check-in object describes a single check-in of the node. The check-in objects have the following keys:

|Key|Definition|
|---|----------|
|`time`|The time of the check-in as a string in ISO 8601 format \(with timezone\).|
|`explanation`|An object mapping between IDs of groups that the node was classified into and explained condition objects that describe why the node matched this group's rule.|
|`transaction_uuid`|A uuid representing a particular Puppet transaction that is submitted by Puppet at classification time. This makes it possible to identify the check-in involved in generating a specific catalog and report.|

The explained condition objects are the node group's rule condition marked up with the node's value and the result of evaluation. Each form in the rule \(that is, each array in the JSON representation of the rule condition\) is replaced with an object that has two keys:

|Key|Definition|
|---|----------|
|`value`|A Boolean that is the result of evaluating this form. At the top level, this is the result of the entire rule condition, but because each sub-condition is marked up with its value, you can use this to understand, say, which parts of an `or` condition were true.|
|`form`|The condition form, with all sub-forms as further explained condition objects.|

Besides the condition markup, the comparison operations of the rule condition have their first argument \(the fact path\) replaced with an object that has both the fact path and the value that was found in the node at that path.

The following example shows the format of an explained condition.

Start with a node group with the following rule:

```javascript
["and", [">=", ["fact", "pressure hulls"], "1"],
        ["=", ["fact", "warp cores"], "0"],
        [">=", ["fact", "docking ports"], "10"]]
```

The following node checks into the classifier:

```javascript
{
  "name": "Deep Space 9",
  "fact": {
    "pressure hulls": "10",
    "docking ports": "18",
    "docking pylons": "3",
    "warp cores": "0",
    "bars": "1"
  }
}
```

When the node checks in for classification, it matches the above rule, so that check-in's explanation object has an entry for the node group that the rule came from. The value of this entry is this explained condition object:

```javascript
{
  "value": true,
  "form": [
    "and",
    {
      "value": true,
      "form": [">=", {"path": ["fact", "pressure hulls"], "value": "3"}, "1"]
    },
    {
      "value": true,
      "form": ["=", {"path": ["fact", "warp cores"], "value": "0"}, "0"]
    },
    {
      "value": true,
      "form": [">" {"path": ["fact", "docking ports"], "value": "18"}, "9"]
    }
  ]
}
```

## GET /v1/nodes/<node\>

Use the `/v1/nodes/<node>` endpoint to retrieve the check-in history for only the specified node.

### Response format

The response is one node object as described above in the GET /v1/nodes documentation, for the specified node. The following example shows a node object:

```javascript
{
  "name": "Deep Space 9",
  "check_ins": [
    {
      "time": "2369-01-04T03:00:00Z",
      "explanation": {
        "53029cf7-2070-4539-87f5-9fc754a0f041": {
          "value": true,
          "form": [
            "and",
            {
              "value": true,
              "form": [">=", {"path": ["fact", "pressure hulls"], "value": "3"}, "1"]
            },
            {
              "value": true,
              "form": ["=", {"path": ["fact", "warp cores"], "value": "0"}, "0"]
            },
            {
              "value": true,
              "form": [">" {"path": ["fact", "docking ports"], "value": "18"}, "9"]
            }
          ]
        }
      }
    }
  ],
  "transaction_uuid": "d3653a4a-4ebe-426e-a04d-dbebec00e97f"
}
```

### Error responses

If the specified node has not checked in, the service returns a `404: Not Found` response, with the usual JSON error response in its body.

