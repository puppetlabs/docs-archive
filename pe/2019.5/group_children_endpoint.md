# Group children endpoint

Use the group children endpoint to retrieve a specified group and its descendents.

## GET /v1/group-children/:id

Use the `/v1/group-children/:id` endpoint to retrieve a specified group and its descendents.

### Request format

The request body must be a JSON object specifying a group and an optional depth indicating how many levels of descendents to return.

-   `depth`: \(optional\) an integer greater than or equal to 0 that limits the depth of trees returned. Zero means return the group with no children.

For example:

```
GET /v1/group-children/00000000-0000-4000-8000-000000000000?depth=2
```

### Response format

The response is a JSON array of group objects, using the following keys:

|Key|Definition|
|---|----------|
|`name`|The name of the node group \(a string\).|
|`id`|The node group's ID, which is a string containing a type-4 \(random\) UUID.|
|`description`|An optional key containing an arbitrary string describing the node group.|
|`environment`|The name of the node group's environment \(a string\), which indirectly defines what classes are available for the node group to set, and is the environment that nodes classified into this node group run under.|
|`environment_trumps`|This is a boolean that changes the behavior of classifications that this node group is involved in. The default behavior is to return a classification-conflict error if the node groups that a node is classified into do not all have the same environment. If this flag is set, then this node group's environment overrides the environments of the other node groups \(provided that none of them also have this flag set\), with no attempt made to validate that the other node groups' classes and class parameters exist in this node group's environment.|
|`parent`|The ID of the node group's parent \(a string\). A node group is not permitted to be its own parent, unless it is the All Nodes group \(which is the root of the hierarchy\). Note that the root group always has the lowest-possible random UUID, `00000000-0000-4000-8000-000000000000`.|
|`rule`|A boolean condition on node properties. When a node's properties satisfy this condition, it's classified into the node group. See Rule condition grammar for more information on how this condition must be structured.|
|`classes`|An object that defines both the classes consumed by nodes in this node group and any non-default values for their parameters. The keys of the object are the class names, and the values are objects describing the parameters. The parameter objects' keys are parameter names, and the values are what the node group sets for that parameter \(always a string\).|
|`deleted`|An object similar the `classes` object that shows which classes and class parameters set by the node group have since been deleted from Puppet. If none of the node group's classes or parameters have been deleted, this key is not present, so checking the presence of this key is an easy way to check whether the node group has references that need updating. The keys of this object are class names, and the values are further objects. These secondary objects always contain the `puppetlabs.classifier/deleted` key, whose value is a Boolean indicating whether the entire class has been deleted from Puppet. The other keys of these objects are parameter names, and the other values are objects that always contain two keys: `puppetlabs.classifier/deleted`, mapping to a boolean indicating whether the specific class parameter has been deleted from Puppet; and `value`, mapping to the string value set by the node group for this parameter \(the value is duplicated for convenience, as it also appears in the `classes` object\).|
|`variables`|An object that defines the values of any top-level variables set by the node group. The object is a mapping between variable names and their values \(which can be any JSON value\).|
|`children`|A JSON array of the group's immediate children. Children of children are included to the optionally-specified depth.|
|`immediate_child_count`|The number of immediate children of the group. Child count reflects the number of children that exist in the classifier, not the number that are returned in the request, which can vary based on permissions and query parameters.|

The following is an example response from a query of the root node group with two children, each with three children. The user has permission to view only `child-1` and `grandchild-5`, which limits the response.

```
[
    {
        "name": "child-1",
        "id": "652227cd-af24-4fd8-96d4-b9b55ca28efb",
        "parent": "00000000-0000-4000-8000-000000000000",
        "environment_trumps": false,
        "rule": ["and", ["=", ["fact", "foo"], "bar"], ["not", ["<", ["fact", "uptime_days"], "31"]]],
        "variables": {},
        "environment": "test",
        "classes": {},
        "children": [
            {
                "name": "grandchild-1",
                "id": "a3d976ad-51d3-4a29-af57-09990f3a2481",
                "parent": "652227cd-af24-4fd8-96d4-b9b55ca28efb",
                "environment_trumps": false,
                "rule": ["and", ["=", ["fact", "foo"], "bar"], ["or", ["~", "name", "db"], ["<", ["fact", "processorcount"], "9"], ["=", ["fact", "operatingsystem"], "Ubuntu"]]],
                "variables": {},
                "environment": "test",
                "classes": {},
                "children": [],
                "immediate_child_count": 0
            },
            {
                "name": "grandchild-2",
                "id": "71905c11-5295-41cf-a143-31b278cfc859",
                "parent": "652227cd-af24-4fd8-96d4-b9b55ca28efb",
                "environment_trumps": false,
                "rule": ["and", ["=", ["fact", "foo"], "bar"], ["not", ["~", ["fact", "kernel"], "SunOS"]]],
                "variables": {},
                "environment": "test",
                "classes": {},
                "children": [],
                "immediate_child_count": 0
            }
        ],
        "immediate_child_count": 2
    },
    {
        "name": "grandchild-5",
        "id": "0bb94f26-2955-4adc-8460-f5ce244d5118",
        "parent": "0960f75e-cdd0-4966-96f6-5e60948a7217",
        "environment_trumps": false,
        "rule": ["and", ["=", ["fact", "foo"], "bar"], ["and", ["<", ["fact", "processorcount"], "16"], [">=", ["fact", "kernelmajversion"], "2"]]],
        "variables": {},
        "environment": "test",
        "classes": {},
        "children": [],
        "immediate_child_count": 0
    }
]
```

### Permissions

The response returned varies based on your permissions.

|**Permissions**|**Response**|
|View the specified group only|An array containing the group and its descendents, ending at the optional depth|
|View descendents of the specified group, but not the group itself|An array starting at the roots of every tree you have permission to view and ending at the optional depth|
|View neither the specified group nor its descendents|An empty array|

### Error responses

Responses and keys returned for group requests depend on the type of error.

**`malformed-uuid`**

If the requested ID is not a valid UUID, the service returns a `400: Bad Request` response using the following keys:

|Key|Definition|
|---|----------|
|`kind`|"malformed-uuid"|
|`details`|The malformed UUID as received by the server.|

**`malformed-number` or `illegal-count`**

If the value of the `depth` parameter is not an integer, or is a negative integer, the service returns a `400: Bad Request` response using one of the following keys:

|Key|Definition|
|---|----------|
|`kind`|"malformed-number" or "illegal-count"|

