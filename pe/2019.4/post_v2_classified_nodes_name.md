---
author: Kate Lopresti <kate.lopresti@puppet.com\>
---

# POST /v2/classified/nodes/<name\>

Use the `/v2/classified/nodes/\<name\>` endpoint to retrieve the classification information for the node with the given name and facts as supplied in the body of the request.

## Request format

The request body can contain a JSON object describing the facts and trusted facts of the node to be classified. The object can have these keys:

|Key|Definition|
|---|----------|
|`fact`|The regular, non-trusted facts of the node. The value of this key is a further object, whose keys are fact names, and whose values are the fact values. Fact values can be a string, number, boolean, array, or object.|
|`trusted`|The trusted facts of the node. The values of this key are subject to the same restrictions as those on the value of the `fact` key.|

## Response format

The response is a JSON object describing the node post-classification, using these keys:

|Key|Definition|
|---|----------|
|`name`|The name of the node \(a string\).|
|`groups`|An array of the groups that this node was classified into. The value of this key is an array of hashes containing the `id` and the `name`, sorted by `name`|
|`environment`|The name of the environment that this node uses, which is taken from the node groups the node was classified into.|
|`classes`|An array of the classes \(strings\) that this node received from the groups it was classified into.|
|`parameters`|An object describing class parameter values for the above classes wherever they differ from the default. The keys of this object are class names, and the values are further objects describing the parameters for just the associated class. The keys of that innermost object are parameter names, and the values are the parameter values, which can be any sort of JSON value.|

This is an example of a response from this endpoint:

```json
{
  "name": "foo.example.com",
  "groups": [{"id": "9c0c7d07-a199-48b7-9999-3cdf7654e0bf",
              "name": "a group"},
             {"id": "96d1a058-225d-48e2-a1a8-80819d31751d",
              "name": "b group"}],
  "environment": "staging",
  "classes": ["apache"],
  "parameters": {
    "apache": {
      "keepalive_timeout": 30,
      "log_level": "notice"
    }
  }
}
```

## Error responses

If the node is classified into multiple node groups that define conflicting classifications for the node, the service returns a 500 Server Error response.

The body of this response contains the usual JSON error object described in the errors documentation.

The `kind` key of the error is "classification-conflict", the `msg` describes generally why this happens, and the `details` key contains an object that describes the specific conflicts encountered.

The keys of these objects are the names of parameters that had conflicting values defined, and the values are arrays of value detail objects. The details object may have between one and all of the following three keys.

|Key|Definition|
|---|----------|
|`environment`|Maps directly to an array of value detail objects \(described below\).|
|`variables`|Contains an object with a key for each conflicting variable, whose values are an array of value detail objects.|
|`classes`|Contains an object with a key for each class that had conflicting parameter definitions, whose values are further objects that describe the conflicts for that class's parameters.|

A value details object describes one of the conflicting values defined for the environment, a variable, or a class parameter. Each object contains these keys:

|Key|Definition|
|---|----------|
|`value`|The defined value, which is a string for environment and class parameters, but for a variable can be any JSON value.|
|`from`|The node group that the node was classified into that caused this value to be added to the node's classification. This group cannot define the value, because it can be inherited from an ancestor of this group.|
|`defined_by`|The node group that actually defined this value. This is often the `from` group, but could instead be an ancestor of that group.|

This example shows a classification conflict error object with node groups truncated for clarity:

```javascript
{
  "kind": "classification-conflict",
  "msg": "The node was classified into multiple unrelated groups that defined conflicting class parameters or top-level variables. See `details` for a list of the specific conflicts.",
  "details": {
    "classes": {
      "songColors": {
        "blue": [
          {
            "value": "Blue Suede Shoes",
            "from": {
              "name": "Elvis Presley",
              "classes": {},
              "rule": ["=", "nodename", "the-node"],
              ...
            },
            "defined_by": {
              "name": "Carl Perkins",
              "classes": {"songColors": {"blue": "Blue Suede Shoes"}},
              "rule": ["not", ["=", "nodename", "the-node"]],
              ...
            }
          },
          {
            "value": "Since You've Been Gone",
            "from": {
              "name": "Aretha Franklin",
              "classes": {"songColors": {"blue": "Since You've Been Gone"}},
              ...
            },
            "defined_by": {
              "name": "Aretha Franklin",
              "classes": {"songColors": {"blue": "Since You've Been Gone"}},
              ...
            }
          }
        ]
      }
    }
  }
}
```

In this example, the conflicting "Blue Suede Shoes" value was included in the classification because the node matched the "Elvis Presley" group \(since that is the value of the "from" key\), but that group doesn't define the "Blue Suede Shoes" value. That value is defined by the "Carl Perkins" group, which is an ancestor of the "Elvis Presley" group, causing the latter to inherit the value from the former. The other conflicting value, "Since You've Been Gone", is defined by the same group that the node matched.

**Parent topic:**[Classification endpoint](classification_endpoint_v2.md)

