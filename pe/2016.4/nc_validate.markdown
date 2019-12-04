---
layout: default
title: "Validation endpoints"
canonical: "/pe/latest/nc_validate.html"
---

These endpoints can be used to validate resources without writing anything to the database.
There is one validation endpoint, for groups.

## POST /v1/validate/group

#### Request format

The request should contain a group object.
The structure of a group object is described in the "request format" section of the [documentation for POSTing a new group](./nc_groups.html#post-v1groups).

#### Response format

If the group is valid, then the server will return a 200 OK response with the validated group as the body.

If a validation error was encountered, the server will return one of the 400-level error responses described under the "Error Responses" section of the [documentation for POSTing a new group](./nc_groups.html#post-v1groups).
