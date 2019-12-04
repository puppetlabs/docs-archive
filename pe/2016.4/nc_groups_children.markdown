---
layout: default
title: "Group children endpoint"
canonical: "/pe/latest/nc_groups_children.html"
---


## GET /v1/group-children/:id

Retrieves a specified group and its descendents.

#### Request format

The request body must be a JSON object specifying a group and an optional depth indicating how many levels of descendents to return.

* `depth`: (optional) an integer greater than or equal to 0 that limits the depth of  trees returned. Zero means return the group with no children.

For example:

GET /v1/group-children/00000000-0000-4000-8000-000000000000?depth=2

#### Response format

The response is a JSON array of [group objects](./nc_groups.html#response-format), with two additional fields:

* `children`: A JSON array of the group's immediate children. Children of children are included to the optionally-specified depth.
* `immediate_child_count`: The number of immediate children of the group. Child count reflects the number of children that exist in the classifier, not the number that are returned in the request, which can vary based on permissions and query parameters.

Below is an example response from a query of the root node group with two children, each with three children. The user has permission to view only  `child-1` and `grandchild-5` which limits the response.

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

#### Permissions

The set of groups returned might vary based on your permissions.

<table>
  <tr>
    <th>If you have view permissions to...</th>
    <th>The response is...</th>
  </tr>
  <tr>
    <td>specified group only</td>
    <td>an array containing the group and its descendents, ending at the optional depth</td>
  </tr>
  <tr>
    <td>descendents of the specified group, but not the group itself</td>
    <td>an array starting at the roots of every tree you have permission to view and ending at the optional depth</td>
  </tr>
  <tr>
    <td>neither the specified group nor its descendents</td>
    <td>an empty array</td>
  </tr>
</table>

#### Error responses

**`malformed-uuid`**

If the requested ID is not a valid UUID, the server returns a 400 Bad Request response with these keys:

* `kind`: `malformed-uuid`
* `details`: the malformed UUID as received by the server

**`malformed-number` or `illegal-count`**

If the value of the `depth` parameter is not an integer, or is a negative integer, the server returns a 400 Bad Request response with one of these keys:

* `kind`: `malformed-number`
* `kind`: `illegal-count`