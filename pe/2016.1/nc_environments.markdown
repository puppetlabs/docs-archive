---
layout: default
title: "Environments endpoint"
canonical: "/pe/latest/nc_environments.html"
---

## GET /v1/environments

Retrieve a list of all environments known to the node classifier.

#### Response format

The response is a JSON array of environment objects.
Each environment object contains one key:

* `name`: the name of the environment (a string).
* `sync_succeeded`: whether the environment synched successfully during the last class synchronization (a Boolean).

#### Error responses

No error responses specific to this request are expected.

## GET /v1/environments/\<name\>

Retrieve the environment with the given name.

#### Response format

If the environment exists, the server will return a 200 response with an environment object as described above, in JSON format.

As the response does not contain any more information about the environment than was specified in the request, the only reason to make this request is to check whether the environment actually exists.

#### Error responses

If the environment with the given name cannot be found, the server will return a 404 Not Found response with an empty body.

## PUT /v1/environments/\<name\>

Create a new environment with the given name.

#### Request format

No further information is required in the request besides the `name` portion of the URL.

#### Response format

If the environment creation is successful, the server will return a 201 Created response whose body is the environment object in JSON format.
[See above](#get-v1environmentsname) for a complete description of an environment object.

#### Error responses

No error responses specific to this operation are expected.
