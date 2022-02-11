# `Groups` endpoint

The `groups` endpoint is used to create, read, update, and delete groups.

A group belongs to an environment, applies classes \(possibly with parameters\), and matches nodes based on rules. Because groups are so central to the classification process, this endpoint is where most of the action is.

## GET /v1/groups

Use the `/v1/groups` endpoint to retrieve a list of all node groups in the node classifier.

### Query parameters

The request accepts these parameters.

|Parameter|Value|
|---------|-----|
|`inherited`|If set to any value besides `0` or `false`, the node group includes the classes, class parameters, configuration data, and variables that it inherits from its ancestors.|

### Response format

The response is a JSON array of node group objects, using these keys:

|Key|Definition|
|---|----------|
|`name`|The name of the node group \(a string\).

|
|`id`|The node group's ID, which is a string containing a type-4 \(random\) UUID. The regular expression used to validate node group UUIDs is `[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}`.

|
|`description`|An optional key containing an arbitrary string describing the node group.

|
|`environment`|The name of the node group's environment \(a string\), which indirectly defines what classes are available for the node group to set, and is the environment that nodes classified into this node group will run under.

|
|`environment_trumps`|This is a boolean that changes the behavior of classifications that this node group is involved in. The default behavior is to return a classification-conflict error if the node groups that a node is classified into do not all have the same environment. If this flag is set, then this node group's environment overrides the environments of the other node groups \(provided that none of them also have this flag set\), with no attempt made to validate that the other node groups' classes and class parameters exist in this node group's environment.

|
|`parent`|The ID of the node group's parent \(a string\). A node group is not permitted to be its own parent, unless it is the **All Nodes** group, which is the root of the hierarchy. Note that the root group always has the lowest-possible random UUID, `00000000-0000-4000-8000-000000000000`.

|
|`rule`|A boolean condition on node properties. When a node's properties satisfy this condition, it's classified into the node group.

|
|`classes`|An object that defines both the classes consumed by nodes in this node group and any non-default values for their parameters. The keys of the object are the class names, and the values are objects describing the parameters. The parameter objects' keys are parameter names, and the values are what the node group sets for that parameter \(always a string\).

|
|`config_data`|An object similar to the `classes` object that specifies parameters that are applied to classes if the class is assigned in the classifier or in puppet code. The keys of the object are the class names, and the values are objects describing the parameters. The parameter objects’ keys are parameter names, and the values are what the group sets for that parameter \(always a string\). This feature is enabled/disabled via the `classifier::allow-config-data` setting. When set to false, this key is stripped from the payload.|
|`deleted`|An object similar to the `classes` object that shows which classes and class parameters set by the node group have since been deleted. If none of the node group's classes or parameters have been deleted, this key will not be present, so checking the presence of this key is an easy way to check whether the node group has references that need updating. The keys of this object are class names, and the values are further objects. These secondary objects always contain the `puppetlabs.classifier/deleted` key, whose value is a Boolean indicating whether the entire class has been deleted. The other keys of these objects are parameter names, and the other values are objects that always contain two keys: `puppetlabs.classifier/deleted`, mapping to a boolean indicating whether the specific class parameter has been deleted; and `value`, mapping to the string value set by the node group for this parameter \(the value is duplicated for convenience, as it also appears in the `classes` object\).

|
|`variables`|An object that defines the values of any top-level variables set by the node group. The object is a mapping between variable names and their values \(which can be any JSON value\).

|
|`last_edited`|The most recent time at which a user committed changes to a node group. This is a time stamp in ISO 8601 format, `YYYY-MM-DDTHH:MM:SSZ`.

|
|`serial_number`|A number assigned to a node group. This number is incremented each time changes to a group are committed. `serial_number` is used to prevent conflicts when multiple users make changes to the same node group at the same time.

|

This example shows a node group object:

```javascript
{
  "name": "Webservers",
  "id": "fc500c43-5065-469b-91fc-37ed0e500e81",
  "last_edited": "2018-02-20T02:36:17.776Z",
  "serial_number": 16,
  "environment": "production",
  "description": "This group captures configuration relevant to all web-facing production webservers, regardless of location.",
  "parent": "00000000-0000-4000-8000-000000000000",
  "rule": ["and", ["~", ["trusted", "certname"], "www"],
                  [">=", ["fact", "total_ram"], "512"]],
  "classes": {
    "apache": {
      "serveradmin": "bofh@travaglia.net",
      "keepalive_timeout": "5"
    }
  },
  "config_data": {
    "puppet_enterprise::profile::console": {"certname": "console.example.com"},
    "puppet_enterprise::profile::puppetdb": {"listen_address": "0.0.0.0"}
  },
  "variables": {
    "ntp_servers": ["0.us.pool.ntp.org", "1.us.pool.ntp.org", "2.us.pool.ntp.org"]
  }
}
```

This example shows a node group object that refers to some classes and parameters that have been deleted:

```javascript
{
  "name": "Spaceship",
  "id": "fc500c43-5065-469b-91fc-37ed0e500e81",
  "last_edited": "2018-03-13T21:37:03.608Z",
  "serial_number": 42,
  "environment": "space",
  "parent": "00000000-0000-4000-8000-000000000000",
  "rule": ["=", ["fact", "is_spaceship"], "true"],
  "classes": {
    "payload": {
      "type": "cubesat",
      "count": "8",
      "mass": "10.64"
    },
    "rocket": {
      "stages": "3"
    }
  },
  "deleted": {
    "payload": {"puppetlabs.classifier/deleted": true},
    "rocket": {
      "puppetlabs.classifier/deleted": false,
      "stages": {
        "puppetlabs.classifier/deleted": true,
        "value": "3"
      }
    }
  },
  "variables": {}
}
```

The entire `payload` class has been deleted, since its deleted parameters object's `puppetlabs.classifier/deleted` key maps to `true`, in contrast to the `rocket` class, which has only had its `stages` parameter deleted.

### Rule condition grammar

Nodes can be classified into groups using rules. This example shows how rule conditions must be structured:

```
    condition  : [ {bool} {condition}+ ] | [ "not" {condition} ] | {operation}
         bool  : "and" | "or"
    operation  : [ {operator} {fact-path} {value} ]
     operator  : "=" | "~" | ">" | ">=" | "<" | "<="
    fact-path  : {field-name} | [ {path-type} {field-name} {path-component}+ ]
    path-type  : "trusted" | "fact"
path-component : field-name | number
    field-name : string
```

For the regex operator `"~"`, the value is interpreted as a Java regular expression. Literal backslashes must be used to escape regex characters in order to match those characters in the fact value.

For the numeric comparison operators \(`">"`, `">="`, `"<"`, and `"<="`\), the fact value \(which is always a string\) is coerced to a number \(either integral or floating-point\). If the value can't be coerced to a number, the numeric operation evaluates to false.

For the fact path, the rule can be either a string representing a top level field \(the only current meaningful value here would be "name" representing the node name\) or a list of strings and indices that represent looking up a field in a nested data structure. When passing a list of strings or indices, the first and second entries in the list must be strings. Subsequent entries can be indices.

Regular facts start with "fact" \(for example, `["fact", "architecture"]`\) and trusted facts start with "trusted" \(for example, `["trusted", "certname"]`\).

### Error responses

`serial_number`

If you commit a node group with a `serial_number` that an API call has previously assigned, the service returns a 409 Conflict response.

## POST /v1/groups

Use the `/v1/groups` endpoint to create a new node group without specifying its ID. When you use this endpoint, the node classifier service randomly generates an ID.

### Request format

The request body must be a JSON object describing the node group to be created. The request uses these keys:

|Key|Definition|
|---|----------|
|`name`|The name of the node group \(a string\).

|
|`environment`|The name of the node group's environment. This key is optional; if it's not provided, the default environment \(`production`\) is used.

|
|`environment_trumps`|Whether this node group's environment should override those of other node groups at classification-time. This key is optional; if it's not provided, the default value of `false` is used.

|
|`description`|A string describing the node group. This key is optional; if it's not provided, the node group has no description and this key doesn't appear in responses.

|
|`parent`|The ID of the node group's parent \(required\).

|
|`rule`|The condition that must be satisfied for a node to be classified into this node group

|
|`variables`|An object that defines the names and values of any top-level variables set by the node group. The keys of the object are the variable names, and the corresponding value is that variable's value, which can be any sort of JSON value. The `variables` key is optional, and if a node group does not define any top-level variables then it may be omitted.

|
|`classes`|An object that defines the classes to be used by nodes in the node group. The `classes` key is required, and at minimum should be an empty object \(\{\}\). The `classes` key also contains the parameters for each class. Some classes have required parameters. This is a two-level object; that is, the keys of the object are class names \(strings\), and each key's value is another object that defines class parameter values. This innermost object maps between class parameter names and their values. The keys are the parameter names \(strings\), and each value is the parameter's value, which can be any kind of JSON value. The `classes` key is *not* optional; if it is missing, the service returns a 400Bad Request response.

|
|`config_data`|An optional object that defines the class parameters to be used by nodes in the group. Its structure is the same as the classes object. If you use a `config_data` key but provide only the class, like `"config_data": {"qux":{}}`, no configuration data is stored.**Note:** This feature is enabled with the `classifier::allow-config-data` setting. When set to false, the presence of this key in the payload results in a 400 response.

|

### Response format

If the node group was successfully created, the service returns a 303 See Other response, with the path to retrieve the created node group in the "location" header of the response.

### Error responses

Responses and keys returned for create group requests depend on the type of error.

`schema-violation`

If any of the required keys are missing or the values of any of the defined keys do not match the required type, the service returns a 400 Bad Requestresponse using these keys:

|Key|Definition|
|---|----------|
|`kind`|"schema-violation"|
|`details`|An object that contains three keys: -   `submitted` — Describes the submitted object.
-   `schema` — Describes the schema that object should conform to.
-   `error` — Describes how the submitted object failed to conform to the schema.

|

`malformed-request`

If the request's body could not be parsed as JSON, the service returns a 400 Bad Request response using these keys:

|Key|Definition|
|---|----------|
|```kind```|"malformed-request"|
|```details```|An object that contains two keys: -   `body` — Holds the request body that was received.
-   `error` — Describes how the submitted object failed to conform to the schema.

|

`uniqueness-violation`

If your attempt to create the node group violates uniqueness constraints \(such as the constraint that each node group name must be unique within its environment\), the service returns a 422 Unprocessable Entity response using these keys:

|**Key**|**Definition**|
|-------|--------------|
|`kind`|"uniqueness-violation"|
|`msg`|Describes which fields of the node group caused the constraint to be violated, along with their values.|
|`details`|An object that contains two keys: -   `conflict` — An object whose keys are the fields of the node group that violated the constraint and whose values are the corresponding field values.
-   `constraintName` — The name of the database constraint that was violated.

|

`missing-referents`

If classes or class parameters defined by the node group, or inherited by the node group from its parent, do not exist in the submitted node group's environment, the service returns a 422 Unprocessable Entity response. In both cases the response object uses these keys:

|Key|Definition|
|---|----------|
|kind|"missing-referents"|
|msg|Describes the error and lists the missing classes or parameters.|
|details|An array of objects, where each object describes a single missing referent, and has the following keys: -   `kind` — "missing-class" or "missing-parameter", depending on whether the entire class doesn't exist, or the class just doesn't have the parameter.
-   `missing` — The name of the missing class or class parameter.
-   `environment` — The environment that the class or parameter is missing from; that is, the environment of the node group where the error was encountered.
-   `group` — The name of the node group where the error was encountered. Note that, due to inheritance, this may not be the group where the parameter was defined.
-   `defined_by` — The name of the node group that defines the class or parameter.

|

`missing-parent`

If the parent of the node group does not exist, the service returns a 422 Unprocessable Entity response. The response object uses these keys:

|Key|Definition|
|---|----------|
|`kind`|"missing-parent"|
|`msg`|Shows the parent UUID that did not exist.|
|`details`|The full submitted node group.|

`inheritance-cycle`

If the request causes an inheritance cycle, the service returns a 422 Unprocessable Entity response. The response object uses these keys:

|Key|Definition|
|---|----------|
|`kind`|"inheritance-cycle"|
|`details`|An array of node group objects that includes each node group involved in the cycle|
|`msg`|A shortened description of the cycle, including a list of the node group names with each followed by its parent until the first node group is repeated.|

## GET /v1/groups/<id\>

Use the `/v1/groups/\<id>` endpoint to retrieve a node group with the given ID.

### Response format

The response is a JSON array of node group objects, using these keys:

|Key|Definition|
|---|----------|
|`name`|The name of the node group \(a string\).

|
|`id`|The node group's ID, which is a string containing a type-4 \(random\) UUID. The regular expression used to validate node group UUIDs is `[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}`.

|
|`description`|An optional key containing an arbitrary string describing the node group.

|
|`environment`|The name of the node group's environment \(a string\), which indirectly defines what classes are available for the node group to set, and is the environment that nodes classified into this node group will run under.

|
|`environment_trumps`|This is a boolean that changes the behavior of classifications that this node group is involved in. The default behavior is to return a classification-conflict error if the node groups that a node is classified into do not all have the same environment. If this flag is set, then this node group's environment overrides the environments of the other node groups \(provided that none of them also have this flag set\), with no attempt made to validate that the other node groups' classes and class parameters exist in this node group's environment.

|
|`parent`|The ID of the node group's parent \(a string\). A node group is not permitted to be its own parent, unless it is the **All Nodes** group, which is the root of the hierarchy. Note that the root group always has the lowest-possible random UUID, `00000000-0000-4000-8000-000000000000`.

|
|`rule`|A boolean condition on node properties. When a node's properties satisfy this condition, it's classified into the node group.

|
|`classes`|An object that defines both the classes consumed by nodes in this node group and any non-default values for their parameters. The keys of the object are the class names, and the values are objects describing the parameters. The parameter objects' keys are parameter names, and the values are what the node group sets for that parameter \(always a string\).

|
|`deleted`|An object similar to the `classes` object that shows which classes and class parameters set by the node group have since been deleted. If none of the node group's classes or parameters have been deleted, this key will not be present, so checking the presence of this key is an easy way to check whether the node group has references that need updating. The keys of this object are class names, and the values are further objects. These secondary objects always contain the `puppetlabs.classifier/deleted` key, whose value is a Boolean indicating whether the entire class has been deleted. The other keys of these objects are parameter names, and the other values are objects that always contain two keys: `puppetlabs.classifier/deleted`, mapping to a boolean indicating whether the specific class parameter has been deleted; and `value`, mapping to the string value set by the node group for this parameter \(the value is duplicated for convenience, as it also appears in the `classes` object\).

|
|`variables`|An object that defines the values of any top-level variables set by the node group. The object is a mapping between variable names and their values \(which can be any JSON value\).

|
|`last_edited`|The most recent time at which a user committed changes to a node group. This is a time stamp in ISO 8601 format, `YYYY-MM-DDTHH:MM:SSZ`.

|
|`serial_number`|A number assigned to a node group. This number is incremented each time changes to a group are committed. `serial_number` is used to prevent conflicts when multiple users make changes to the same node group at the same time.

|

### Error responses

If the node group with the given ID cannot be found, the service returns 404 Not Found and `malformed-uuid` responses . The body will be a generic 404 error response as described in the errors documentation.

`serial_number`

If you commit a node group with a `serial_number` that an API call has previously assigned, the service returns a 409 Conflict response.

**Related information**  


[Node classifier errors](node_classifier_errors.md#)

## PUT /v1/groups/<id\>

Use the `/v1/groups/<id>` to create a node group with the given ID.

CAUTION:

Any existing node group with the given ID is overwritten.

It is possible to overwrite an existing node group with a new node group definition that contains deleted classes or parameters.

### Request format

The request body must be a JSON object describing the node group to be created. The request uses these keys:

|Key|Definition|
|---|----------|
|`name`|The name of the node group \(a string\).

|
|`environment`|The name of the node group's environment. This key is optional; if it's not provided, the default environment \(`production`\) is used.

|
|`environment_trumps`|Whether this node group's environment should override those of other node groups at classification-time. This key is optional; if it's not provided, the default value of `false` is used.

|
|`description`|A string describing the node group. This key is optional; if it's not provided, the node group has no description and this key doesn't appear in responses.

|
|`parent`|The ID of the node group's parent \(required\).

|
|`rule`|The condition that must be satisfied for a node to be classified into this node group

|
|`variables`|An object that defines the names and values of any top-level variables set by the node group. The keys of the object are the variable names, and the corresponding value is that variable's value, which can be any sort of JSON value. The `variables` key is optional, and if a node group does not define any top-level variables then it may be omitted.

|
|`classes`|An object that defines the classes to be used by nodes in the node group. The `classes` key is required, and at minimum should be an empty object \(\{\}\). The `classes` key also contains the parameters for each class. Some classes have required parameters. This is a two-level object; that is, the keys of the object are class names \(strings\), and each key's value is another object that defines class parameter values. This innermost object maps between class parameter names and their values. The keys are the parameter names \(strings\), and each value is the parameter's value, which can be any kind of JSON value. The `classes` key is *not* optional; if it is missing, the service returns a 400Bad Request response.

|

### Response format

If the node group is successfully created, the service returns a 201 Created response, with the node group object \(in JSON\) as the body. If the node group already exists and is identical to the submitted node group, then the service takes no action and returns a 200 OK response, again with the node group object as the body.

### Error responses

If the requested node group object contains the `id` key, and its value differs from the UUID specified in the request's path, the service returns a 400 Bad Request response.

The response object uses these keys:

|**Key**|**Definition**|
|`kind`|"conflicting-ids"|
|`details`|An object that contains two keys: -   `submitted` — Contains the ID submitted in the request body.
-   `fromUrl` — Contains the ID taken from the request URL.

|

In addition, this operation can produce the general `malformed-error` response and any response that could also be generated by the POST group creation endpoint.

## POST /v1/groups/<id\>

Use the `/v1/groups/<id>` endpoint to update the name, environment, parent, rule, classes, class parameters, configuration data, and variables of the node group with the given ID by submitting a node group delta.

### Request format

The request body must be JSON object describing the delta to be applied to the node group.

The `classes`, `config_data`, `variables`, and `rule` keys of the delta will be merged with the node group, and then any keys of the resulting object that have a null value will be deleted. This allows you to remove classes, class parameters, configuration data, variables, or the rule from the node group by setting them to null in the delta.

If the delta has a rule key that’s set to a new value or nil, it will be updated wholesale or removed from the group accordingly.

The `name`, `environment`, `description`, and `parent` keys, if present in the delta, will replace the old values wholesale with their values.

The `serial_number`key is optional. If you update a node group and provide the `serial_number` in the payload, and the `serial_number` is not the current one for that group, the service returns a 409 Conflict response. To bypass this check, omit the `serial_number`.

Note that the root group’s rule cannot be edited; any attempts to do will raise a 422 Unprocessable Entity response.

In the following examples, a delta is merged with a node group to update the group.

Node group:

```
{
  "name": "Webservers",
  "id": "58463036-0efa-4365-b367-b5401c0711d3",
  "environment": "staging",
  "parent": "00000000-0000-4000-8000-000000000000",
  "rule": ["~", ["trusted", "certname"], "www"],
  "classes": {
    "apache": {
      "serveradmin": "bofh@travaglia.net",
      "keepalive_timeout": 5
    },
    "ssl": {
      "keystore": "/etc/ssl/keystore"
    }
  },
  "variables": {
    "ntp_servers": ["0.us.pool.ntp.org", "1.us.pool.ntp.org", "2.us.pool.ntp.org"]
  }
}
```

Delta:

```
{
  "name": "Production Webservers",
  "id": "58463036-0efa-4365-b367-b5401c0711d3",
  "environment": "production",
  "parent": "01522c99-627c-4a07-b28e-a25dd563d756",
  "classes": {
    "apache": {
      "serveradmin": "roy@reynholm.co.uk",
      "keepalive_timeout": null
    },
    "ssl": null
  },
  "variables": {
    "dns_servers": ["dns.reynholm.co.uk"]
  }
}
```

Updated group:

```
{
  "name": "Production Webservers",
  "id": "58463036-0efa-4365-b367-b5401c0711d3",
  "environment": "production",
  "parent": "01522c99-627c-4a07-b28e-a25dd563d756",
  "rule": ["~", ["trusted", "certname"], "www"],
  "classes": {
    "apache": {
      "serveradmin": "roy@reynholm.co.uk"
    }
  },
  "variables": {
    "ntp_servers": ["0.us.pool.ntp.org", "1.us.pool.ntp.org", "2.us.pool.ntp.org"],
    "dns_servers": ["dns.reynholm.co.uk"]
  }
}
```

Note that the `ssl` class was deleted because its entire object was mapped to null, whereas for the `apache` class only the `keepalive_timeout` parameter was deleted.

### Deleted classes and class parameters

If the node group definition contains classes and parameters that have been deleted it is still possible to update the node group with those parameters and classes. Updates that don’t increase the number of errors associated with a node group are allowed.

### Error responses

The response object uses these keys:

|Key|Definition|
|---|----------|
|kind|"conflicting-ids"|
|details|An object that contains two keys: -   `submitted` — Contains the ID submitted in the request body.
-   `fromUrl` — Contains the ID taken from the request URL.

|

If the requested node group object contains the `id` key, and its value differs from the UUID specified in the request’s path, the service returns a 400 Bad Request response.

In addition, this operation can produce the general malformed-error response and any response that could also be generated by the POST group creation endpoint.

422 responses to POST requests can include errors that were caused by the node group’s children, but a node group being created with a PUT request cannot have any children.

## DELETE /v1/groups/<id\>

Use the `/v1/groups/\<id\>` endpoint to delete the node group with the given ID.

### Response format

If the delete operation is successful, the sever returns a 204 No Content response.

### Error responses

In addition to the general `malformed-uuid` response, if the node group with the given ID does not exist, the service returns a 404 Not Found response, as described in the errors documentation.

**`children-present`**

The service returns a 422 Unprocessable Entity and rejects the delete request if the node group that is being deleted has children.

The response object uses these keys:

|Key|Definition|
|---|----------|
|`kind`|"children-present"|
|`msg`|Explains why the delete was rejected and names the children.|
|`details`|Contains the node group in question along with all of its children.|

**Related information**  


[Node classifier errors](node_classifier_errors.md#)

## POST /v1/groups/<id\>/pin

Use the `/v1/groups/<id>/pin` endpoint to pin nodes to the group with the given ID.

### Request format

You can provide the names of the nodes to pin in two ways.

-   As the value of the `nodes` query parameter. For multiple nodes, use a comma-separated list format.

    For example:

    `POST /v1/groups/58463036-0efa-4365-b367-b5401c0711d3/pin?nodes=foo%2Cbar%2Cbaz`

    This request pins the nodes `foo`, `bar`, and `baz` to the group.

-   In the body of a request. In the `nodes` field of a JSON object, specify the node name as the value. For multiple nodes, use a JSON array.

    For example:

    `{"nodes": ["foo", "bar", "baz"]}`

    This request pins the nodes `foo`, `bar`, and `baz` to the group.


It's easier to use the query parameter method. However, if you want to affect a large number of nodes at once, the query string might get truncated. Strings are truncated if they exceed 8,000 characters. In such cases, use the second method. The request body in the second method is allowed to be many megabytes in size.

### Response format

If the pin is successful, the service returns a 204 No Content response with an empty body.

### Error responses

This endpoint uses the following error responses.

If you don't supply the `nodes` query parameter or a request body, the service returns a 400 Malformed Request response, using these keys:

|Key|Definition|
|---|----------|
|`kind`|"missing-parameters"|
|`msg`|Explains the missing `nodes` query parameter.|

If you supply a request body that is not valid JSON, the service returns a 400 Malformed Request response. The response object uses these keys:

|Key|Definition|
|---|----------|
|`kind`|"malformed-request"|
|`details`|An object with a `body` key containing the body as received by the service, and an `error` field containing a string describing the error encountered when trying to parse the request's body.|

If the request's body is valid JSON, but the payload is not an object with just the `nodes` field and no other fields, the service returns a 400 Malformed Request response. The response object uses these keys:

|Key|Definition|
|---|----------|
|`kind`|"schema-violation"|
|`msg`|Describes the difference between what was submitted and the required format.|

## POST /v1/groups/<id\>/unpin

Use the `/v1/groups/<id>/unpin` endpoint to unpin nodes from the group with the given ID.

**Note:** Nodes not actually pinned to the group can be specified without resulting in an error.

### Request format

You can provide the names of the nodes to pin in two ways.

-   As the value of the `nodes` query parameter. For multiple nodes, use a comma-separated list format.

    For example:

    `POST /v1/groups/58463036-0efa-4365-b367-b5401c0711d3/unpin?nodes=foo%2Cbar%2Cbaz`

    This request unpins the nodes `foo`, `bar`, and `baz` from the group. If any of the specified nodes were not pinned to the group, they are ignored.

-   In the body of a request. In the `nodes` field of a JSON object, specify the node name as the value. For multiple nodes, use a JSON array.

    For example:

    `{"nodes": ["foo", "bar", "baz"]}`

    This request unpins the nodes `foo`, `bar`, and `baz` from the group. If any of the specified nodes were not pinned to the group, they are ignored.


It's easier to use the query parameter method. However, if you want to affect a large number of nodes at once, the query string might get truncated. Strings are truncated if they exceed 8,000 characters. In such cases, use the second method. The request body in the second method is allowed to be many megabytes in size.

### Response format

If the unpin is successful, the service returns a 204 No Content response with an empty body.

### Error responses

If you don't supply the `nodes` query parameter or a request body, the service returns a 400 Malformed Request response. The response object uses these keys:

|Key|Definition|
|---|----------|
|`kind`|"missing-parameters"|
|`msg`|Explains the missing `nodes` query parameter.|

If a request body is supplied but it is not valid JSON, the service will return a 400 Malformed Request response. The response object uses these keys:

|Key|Definition|
|---|----------|
|`kind`|"malformed-request"|
|`details`|An object with a `body` key containing the body as received by the service, and an `error` field containing a string describing the error encountered when trying to parse the request's body.|

If the request's body is valid JSON, but the payload is not an object with just the `nodes` field and no other fields, the service returns a 400 Malformed Request response. The response object uses these keys:

|Key|Definition|
|---|----------|
|`kind`|"schema-violation"|
|`msg`|Describes the difference between what was submitted and the required format.|

