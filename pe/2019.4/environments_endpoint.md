# Environments endpoint

Use the `environments` endpoint to retrieve information about environments in the node classifier. The output tells you either which environments are available or whether a named environment exists. The output can be helpful when creating new node groups, which must be associated with an environment. The node classifier gets its information about environments from Puppet, so do not use this endpoint to create, update, or delete them.

**Parent topic:**[Node classifier API v1](node_classifier_service_api.md)

## GET /v1/environments

Use the `/v1/environments` endpoint to retrieve a list of all environments known to the node classifier.

### Response format

The response is a JSON array of environment objects, using the following keys:

|Key|Definition|
|---|----------|
|`name`|The name of the environment \(a string\).|
|`sync_succeeded`|Whether the environment synched successfully during the last class synchronization \(a Boolean\).|

### Error responses

No error responses specific to this request are expected.

## GET /v1/environments/<name\>

Use the `/v1/environments/\<name\>` endpoint to retrieve the environment with the given name. The main use of this endpoint is to check if an environment actually exists.

### Response format

If the environment exists, the service returns a `200` response with an environment object in JSON format.

### Error responses

If the environment with the given name cannot be found, the service returns a `404: Not Found` response with an empty body.

## PUT /v1/environments/<name\>

Use the `/v1/environments/\<name\>` endpoint to create a new environment with the given name.

### Request format

No further information is required in the request besides the `name` portion of the URL.

### Response format

If the environment is created successfully, the service returns a `201: Created` response whose body is the environment object in JSON format.

### Error responses

No error responses specific to this operation are expected.

