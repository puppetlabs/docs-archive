---
layout: default
title: "Using the Razor API"
canonical: "/pe/latest/razor_reference.html"

---

The Razor API is REST-based. The default URL for the API entrypoint is `https://razor:8151/api`.

The API uses JSON exclusively, and all requests must set the HTTP `Content-Type` header to `application/json` and must `Accept` `application/json` in the response.

The Razor API is stable, and clients can expect operations that work against this version of the API to work against future versions of the API. However, we might add information or functionality to the API, so clients must ignore anything in responses they receive from the server that they don't understand.

> **Note**: The `/svc` namespace is an internal namespace used for communication with the iPXE client, the Microkernel, and other internal components of Razor. This namespace is not enumerated under `/api` and has no stability promises. We recommend making `/svc` URLs available only to that part of the network that contains nodes that need to be provisioned.

## Common attributes

Two attributes are commonly used to identify objects:

* `id` can be used as a GUID for an object. A `GET` request against a URL
with an `id` attribute produces a representation of the object.
* `name` is used for a short, human-readable reference to an object,
generally only unique amongst objects of the same type on the same server.
 * `rel`: a "spec URL" that indicates the type of contained data.  Use this to discover the endpoint that you want to follow, rather than the `name`.

## `/api` reference

By default, API calls are sent over HTTPS with TLS/SSL. The default URL for
the API entrypoint is `https://razor:8151/api`. Everything underneath
`/api` is part of the public API and adheres to Razor's stability
guarantees.

The top-level entrypoint serves as the start for navigating through the
Razor command and query facilities. The response is a JSON object with the
following keys:

* `collections`: read-only queries available on this server.
* `commands`: the commands available on this server.
* `version`: the version of Razor that is running on the server. The
    version should only be used for diagnostic purposes and for bug
    reporting, and never to decide whether the server supports a certain
    operation or not.

Each of those keys contains a JSON array, with a sequence of JSON objects
that have the following keys:

 * `name`: a human-readable label.
 * `id`: the URL of this entity.

## Collections

In addition to the supported commands above, a `GET /api` request returns a
list of supported collections in the `collections` array. Each entry
contains at minimum the following keys:

* `name`: A human-readable name for the collection.
* `id`: The endpoint through which the collection can be retrieved (via `GET`).
* `rel`: The type of the collection.

A `GET` request to the `id` of a collection returns a JSON object; the
`spec` property of that object indicates the type of collection, the
`total` indicates how many items there are in the collection in total (not
just how many were returned by the query), and `items` is the actual list
of items in the collection, a JSON array of objects. Each object has the
following properties:

* `id`: A URL that uniquely identifies the object. A `GET` request to this URL provides further detail about the object.
* `spec`: A URL that identifies the type of the object.
* `name`: A human-readable name for the object.

### Object details

Performing a `GET` request against the `id` of an item in a collection
returns further detail about that object. Different types of objects
provide different properties. For example, here is a sample tag listing:

    {
      "spec": "http://api.puppetlabs.com/razor/v1/collections/tags/member",
      "id": "https://razor:8151/api/collections/tags/anything",
      "name": "anythin",
      "rule": [ "=", 1, 1],
      "nodes": {
        "id": "http://razor:8151/api/collections/tags/anything/nodes",
        "count": 2,
        "name": "nodes"
       },
      "policies": {
        "id": "http://razor:8151/api/collections/tags/anything/policies",
        "count": 0,
        "name": "policies"
       }
    }


References to other resources are represented as a single JSON object (in
the case of a one-to-one relationship) or an array of JSON objects (for a
one-to-many or many-to-many relationship). Each JSON object contains the
following fields:

* `id`: A URL that uniquely identifies the associated object or collection of objects.
* `spec`: The type of the associated object.
* `name`: A human-readable name for the object.
* `count`: The number of objects in the associated collection.

### Querying the node collection

You can query nodes based on the following criteria:

* `hostname`: A regular expression to match against hostnames. The results
  include partial matches, so `hostname=example` returns all nodes whose
  hostnames include `example`.
* fields stored in `hw_info`: `mac`, `serial`, `asset`, and `uuid`.

For example, the following queries the UUID to return the associated node:

	/api/collections/nodes?uuid=9ad1e079-b9e3-347c-8b13-9b42cbf53a14'

	{
      "items": [
       {
          "id": "https://razor.example.com:8151/api/collections/nodes/node14",
          "name": "node14",
          "spec": "http://api.puppetlabs.com/razor/v1/collections/nodes/member"
        }],
      "spec": "http://api.puppetlabs.com/razor/v1/collections/nodes"
	}


### Paging collections
The `nodes` and `events` collections are paginated. `GET` requests for them

may include the following parameters to limit the number of items returned:

* `limit`: Only return this many items.
* `start`: Return items starting at `start`.

## Commands

The list of commands that the Razor server supports is returned as part of
a request to `GET /api` in the `commands` array. Clients can identify
commands using the `rel` attribute of each entry in the array, and should
make their POST requests to the URL given in the `id` attribute.

The `id` URL for each command supports the following HTTP methods:
* `GET`: retrieve information about the command, such as a help text and
  machine-readable information about the parameters this command takes
* `POST`: execute the command. Command parameters are supplied in a JSON
  document in the body of the `POST` request

Commands are generally asynchronous and return a status code of `202
Accepted` on success. The response from a command generally has the form

~~~
   {
     "result":"Policy win2012r2 disabled",
     "command":"http://razor:8088/api/collections/commands/74"
   }
~~~

Here, `result` is a human-readable explanation of what the command did, and
`command` points into the collection of all the commands that were ever run
against this server. Performing a `GET` against the `command` URL provides
additional information about the execution of this command, such as the
status of the command, the parameters sent to the server, and details about
errors.

> **Tip:** Most Razor client commands allow positional arguments, which can save keystrokes. For more information, see [Using positional arguments with Razor client commands](./razor_client.html#using-positional-arguments-with-razor-client-commands).



***
