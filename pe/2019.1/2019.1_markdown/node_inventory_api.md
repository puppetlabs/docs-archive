---
author: Kate Lopresti <kate.lopresti@puppet.com\>
---

# Node inventory API

These are the endpoints for the node inventory v1 API.

**Related information**  


[API index](api_index.md#)

## Node inventory API: forming requests

Make well-formed HTTP\(S\) requests to the Puppet inventory service API.

By default, the node inventory service listens on port `8143`, and all endpoints are relative to the `/inventory/v1` path. For example, the full URL for the `/command/create-connection` endpoint on localhost is `https://localhost:8143/inventory/v1/command/create-connection`.

### Token authentication

All requests made to the inventory service API require authentication. You do this for each endpoint in the service using user authentication tokens contained within the `X-Authentication` request header.

### Example token usage: create a connection entry

To post a new connection entry to the inventory service when running on localhost, first generate a token with the puppet-access tool. Then copy that token and replace `<TOKEN>` in the following curl command.

```
curl -k -H 'X-Authentication:<TOKEN>' \
    -H "Content-Type: application/json" \
    https://localhost:8143/inventory/v1/command/create-connection -X POST \
    -d '{"certnames": ["new.node"], "type": "ssh", "parameters": {"tmpdir": "/tmp", "port": 1234}, "sensitive_parameters": {"username": "root", "password": "password"}, "duplicates": "replace"}'
```

### Example token usage: query connections for a certname

To query the `/query/connections` endpoint, use the same token and header pattern.

```
curl -k -X GET https://localhost:8143/inventory/v1/query/connections?certname="new.node" \
     -H 'X-Authentication:<TOKEN>'
```

**Related information**  


[Token-based authentication](rbac_token_auth_intro.md#)

## POST /command/create-connection

Create a new connection entry in the inventory service database.

Connection entries contain connection options, such as credentials, which are used to connect to the certnames provided in the payload via the provided connection type.

This endpoint also inserts each of the provided certnames into PuppetDB with an empty fact set, if they are not already present. After certnames have been added to PuppetDB, you can view them from the **Nodes** page in the Puppet Enterprise console. You can also add them to an inventory node list when you set up a job to run tasks.

**Important:** When the Puppet orchestrator targets a certname to run a task, it first considers the value of the `hostname` key present in the `parameters`, if available. Otherwise, it uses the value of the `certnames` key as the hostname. A good practice is to include a `hostname` key only when the hostname differs from the `certname`. Do not include a hostname key for multiple certname connection entries.

### Request format

The request body must be a JSON object.

-   **`certnames`**

    *required* Array containing the list of certnames to associate with connection information.

-   **`type`**

    *required* String containing either `ssh` or `winrm`. Instructs bolt-server which connection type to use to access the node when running a task.

-   **`parameters`**

    *required* Object containing arbitrary key/value pairs. The necessary parameters for connecting to the provided certnames.

    CAUTION:

    A `hostname` key entered here takes precedence over the values in `certnames` key.

-   **`sensitive_parameters`**

    *required* Object containing arbitrary key/value pairs. The necessary sensitive data for connecting to the provided certnames, stored in an encrypted format.

-   **`duplicates`**

    *required* String containing either `error` or `replace`. Instructs how to handle cases where one or more provided certnames conflict with existing certnames stored in the inventory connections database. `error` results in a `409`response if any certnames are duplicates. `replace` overwrites the existing certnames if there are conflicts.


### Request examples

```json
{
  "certnames": ["avery.gooddevice", "amediocre.device"],
  "type": "ssh",
  "parameters": {
    "tmpdir": "/tmp",
    "port": 1234
  },
  "sensitive_parameters": {
    "username": "root",
    "password": "password"
  },
  "duplicates": "replace"
}
```

```
curl -k -H 'X-Authentication:<AUTHENTICATION TOKEN>'\
    -H "Content-Type: application/json" \
    https://<MASTER-HOST>:8143/inventory/v1/command/create-connection -X POST \
    -d '{"certnames": ["avery.gooddevice","amediocre.device"], "type": "ssh", "parameters": {"tmpdir": "/tmp", "port": 1234}, "sensitive_parameters": {"username": "root", "password": "password"}, "duplicates": "replace"}'
```

### Response format

If the request is valid according to the schema and the entry is successfully recorded in the database, the server returns a `201` response. The response has the same format for single and multiple certname entries.

The response is a JSON object containing the `connection_id`.

-   **`connection_id`**

    A unique identifier that an be used to reference the record.


### Response example

```json
{
  "connection_id": "3c4df64f-7609-4d31-9c2d-acfa52ed66ec"
}
```

## POST /command/delete-connection

Delete certnames from all associated connection entries in the inventory service database and update those certname entries in the PuppetDB with the flag `preserve: false`.

### Request format

The request body must be a JSON object containing a certnames key.

-   **`certnames`**

    *required* Array containing the list of certnames to be removed.


### Request examples

```
{
  "certnames": ["avery.gooddevice", "amediocre.device"]
}
```

```
curl -k -H 'X-Authentication:<AUTHENTICATION TOKEN>'\
    -H "Content-Type: application/json" \
    https://<MASTER-HOST>:8143/inventory/v1/command/delete-connection -X POST \
    -d '{"certnames": ["avery.gooddevice","amediocre.device"]}'
```

### Response format

If the request matches the schema and is processed successfully, the service responds with a `204` status code and no body payload. If the user is unauthorized, the service responds with a `403` error code.

## Command endpoint error responses

General format of error responses.

Every error response from the inventory service is a JSON response. Each response is an object the contains the following keys:

-   **`kind`**

    The kind of error encountered.

-   **`msg`**

    The message associated with the error.

-   **`details`**

    A hash with more information about the error.


For example, if the request is missing the required content type headers, the following error occurs:

```json
{
  "kind" : "puppetlabs.inventory/not-acceptable",
  "msg" : "accept must include content-type json",
  "details" : ""
}
```

### `kind` error responses

For this endpoint, the `kind` key of the error displays the conflict.

-   **`puppetlabs.inventory/unknown-error`**

    If an unknown error occurs during the request, the server returns a `500` response.

-   **`puppetlabs.inventory/not-acceptable`**

    If the content provided to the API contains an "accepts" header which does not allow for JSON, the server returns a `406` response.

-   **`puppetlabs.inventory/unsupported-type`**

    If the content provided to the API contains a "content-type" header other than JSON, the server returns a `416` response.

-   **`puppetlabs.inventory/json-parse-error`**

    If there is an error while processing the request body, the server returns a `400` response.

-   **`puppetlabs.inventory/schema-validation-error`**

    If there is a violation of the required format for the request body, the server returns a `400` response.

-   **`puppetlabs.inventory/not-permitted`**

    If the user requesting an action does not have the necessary permissions to do so, the server returns a `403` response.

-   **`puppetlabs.inventory/duplicate-certnames`**

    If the `duplicates` parameter is not set, or is set as `error`, and one or more of the certnames in the request body already exist within the inventory, the server returns a `409` response.


## GET /query/connections

List all the connections entries in the inventory database.

### Request format

The request body must be a JSON object.

-   **`certname`**

    *optional* String that represents the single certname to retrieve.

-   **`sensitive`**

    *optional* String or boolean that instructs whether to return sensitive parameters for each connection in the response. This parameter is gated by permission validation.

-   **`extract`**

    *optional* Array of keys to return for each certname. `connection_id` is always returned, regardless of whether it is included. When `extract` is not present in the body, all keys are returned.

    **Tip:** In order to return sensitive parameters in the extract list, the `sensitive` query parameter must be present and resolve to true. Otherwise, they are excluded.


### Response format

The response is a JSON object containing the known connections. The following keys are used:

-   **`items`**

    Contains an array of all the known connections matching the specified \(or not specified\) filtering criteria. Each item under `items` is an object with the following keys:

    -   **`connection_id`**

        String that is the unique identifier for the connections entry.

    -   **`certnames`**

        Array of strings that contains the certnames of the matching connections entries.

    -   **`type`**

        String that describes the type of connection for the given information. For example, `ssh` or `winrm`.

    -   **`parameters`**

        Object containing arbitrary key/value pairs and describes connection options for the entry.

    -   **`sensitive_parameters`**

        *when specified and permitted* An object that contains arbitrary key/value pairs and describes the sensitive connection options for the entry.


### Response examples

A response for a request to list all the connections entries in the inventory database: `GET /query/connections`

```json
{
  "items": [
    {
      "connection_id": "3c4df64f-7609-4d31-9c2d-acfa52ed66ec",
      "certnames": ["devices.arecool", "dont.youthink"],
      "type": "ssh",
      "parameters": {
        "tmpdir": "/tmp",
        "port": 1234
      }
    },
    {
      "connection_id": "4932bfe7-69c4-412f-b15c-ac0a7c2883f1",
      "certnames": ["managing.devices", "is.evencooler"],
      "type": "winrm",
      "parameters": {
        "tmpdir": "/tmp",
        "port": 1234
      }
    }
  ]
}
```

A response for a request to list a specific certname: `GET /query/connections?certname="averygood.device"`

```json
{
  "items": [
    {
      "connection_id": "3c4df64f-7609-4d31-9c2d-acfa52ed66ec",
      "certnames": ["averygood.device"],
      "type": "ssh",
      "parameters": {
        "tmpdir": "/tmp",
        "port": 1234
      }
    }
  ]
}
```

A response for request to list a specific certname and its sensitive parameters: `GET /query/connections?certname="averygood.device"&sensitive=true example`

```json
{
  "items": [
    {
      "connection_id": "3c4df64f-7609-4d31-9c2d-acfa52ed66ec",
      "certnames": ["averygood.device"],
      "type": "ssh",
      "parameters": {
        "tmpdir": "/tmp",
        "port": 1234
      },
      "sensitive_parameters": {
        "username": "johnjohnjimmyjohn",
        "password": "C7b0$ls8ltO"
      }
    }
  ]
}
```

A response for request to list a specific certname and its value for the `type` key: `GET /query/connections?certname="averygood.device"&extract=["type"]`

```
{
  "items": [
    {
      "connection_id": "3c4df64f-7609-4d31-9c2d-acfa52ed66ec",
      "certnames": ["averygood.device"],
      "type": "ssh"
    }
  ]
}
```

## POST /query/connections

Retrieve inventory connections in bulk via certname.

### Request format

The request body must be a JSON object.

-   **`certnames`**

    Array containing the list of certnames to retrieve from the inventory database. If this key is not included, all connections are returned.

-   **`extract`**

    Array of keys to return for each certname. `connection_id` is always returned, regardless of whether it is included. When `extract` is not present in the body, all keys are returned.

    **Tip:** In order to return sensitive parameters in the extract list, the `sensitive` query parameter must be present and resolve to true. Otherwise, they are excluded.

-   **`sensitive`**

    *optional* String or boolean that instructs whether to return sensitive parameters for each connection in the response. This parameter is gated by permission validation.


### Response format

The response is a JSON object containing the known connections. The following keys are used:

-   **`items`**

    Contains an array of all the known connections matching the specified \(or not specified\) filtering criteria. Each item under `items` is an object with the following keys:

    -   **`connection_id`**

        String that is the unique identifier for the connections entry.

    -   **`certnames`**

        Array of strings that contains the certnames of the matching connections entries.

    -   **`type`**

        String that describes the type of connection for the given information. For example, `ssh` or `winrm`.

    -   **`parameters`**

        Object containing arbitrary key/value pairs and describes connection options for the entry.

    -   **`sensitive_parameters`**

        *when specified and permitted* An object that contains arbitrary key/value pairs and describes the sensitive connection options for the entry.


### Request and response examples

An empty request body.

```
{}
```

A response for an empty request.

```json
{
  "items": [
    {
      "connection_id": "3c4df64f-7609-4d31-9c2d-acfa52ed66ec",
      "certnames": ["devices.arecool", "dont.youthink"],
      "type": "winrm",
      "parameters": {
        "tmpdir": "/tmp",
        "port": 1234
      }
    },
    {
      "connection_id": "4932bfe7-69c4-412f-b15c-ac0a7c2883f1",
      "certnames": ["managing.devices", "is.evencooler"],
      "type": "ssh",
      "parameters": {
        "tmpdir": "/tmp",
        "port": 1234
      }
    }
  ]
}
```

A certnames request body.

```
{
  "certnames": ["averygood.device"]
}
```

A response for a certnames request.

```
{
  "items": [
    {
      "connection_id": "3c4df64f-7609-4d31-9c2d-acfa52ed66ec",
      "certnames": ["averygood.device"],
      "type": "ssh",
      "parameters": {
        "tmpdir": "/tmp",
        "port": 1234
      }
    }
  ]
}
```

A request to list a specific certname and its sensitive parameters: `/query/connections?sensitive=true`

```
{
  "certnames": ["averygood.device"],
  "extract": ["certnames", "sensitive_parameters"]
}
```

A response for a request for a specific certname and its sensitive parameters.

```
{
  "items": [
    {
      "connection_id": "3c4df64f-7609-4d31-9c2d-acfa52ed66ec",
      "certnames": ["averygood.device"],
      "sensitive_parameters": {
        "username": "johnjohnjimmyjohn",
        "password": "C7b0$ls8ltO"
      }
    }
  ]
}
```

## Query endpoint error responses

General format of error responses.

Every error response from the inventory service is a JSON response. Each response is an object the contains the following keys:

-   **`kind`**

    The kind of error encountered.

-   **`msg`**

    The message associated with the error.

-   **`details`**

    A hash with more information about the error.


For example, if the request is missing the required content type headers, the following error occurs:

```json
{
  "kind" : "puppetlabs.inventory/not-acceptable",
  "msg" : "accept must include content-type json",
  "details" : ""
}
```

### `kind` error responses

For this endpoint, the `kind` key of the error displays the conflict.

-   **`puppetlabs.inventory/unknown-error`**

    If an unknown error occurs during the request, the server returns a `500` response.

-   **`puppetlabs.inventory/not-acceptable`**

    If the content provided to the API contains an "accepts" header which does not allow for JSON, the server returns a `406` response.

-   **`puppetlabs.inventory/unsupported-type`**

    If the content provided to the API contains a "content-type" header other than JSON, the server returns a `416` response.

-   **`puppetlabs.inventory/json-parse-error`**

    If there is an error while processing the request body, the server returns a `400` response.

-   **`puppetlabs.inventory/schema-validation-error`**

    If there is a violation of the required format for the request body, the server returns a `400` response.


