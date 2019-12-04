# Node classifier errors

Familiarize yourself with error responses to make working the node classifier service API easier.

## Error response description

Errors from the node classifier service are JSON responses.

Error responses contain these keys:

|Key|Definition|
|---|----------|
|`kind`|A string classifying the error. It should be the same for all errors that have the same kind of thing in their `details` key.|
|`msg`|A human-readable message describing the error, suitable for presentation to the user.|
|`details`|Additional machine-readable information about the error condition. The format of this key's value will vary between kinds of errors but will be the same for any given error kind.|

## Internal server errors

Any endpoint may return a `500: Internal Server Error` response in addition to its usual responses. There are two kinds of internal server error responses: `application-error` and `database-corruption`.

An `application-error` response is a catchall for unexpected errors. The `msg` of an `application-error` 500 contains the underlying error's message first, followed by a description of other information that can be found in details. The `details` contain the error's stack trace as an array of strings under the `trace` key, and might also contain `schema`, `value`, and `error` keys if the error was caused by a schema validation failure.

A `database-corruption` 500 response occurs when a resource that is retrieved from the database fails to conform to the schema expected of it by the application. This is probably just a bug in the software, but it could potentially indicate either genuine corruption in the database or that a third party has changed values directly in the database. The `msg` section contains a description of how the database corruption could have occurred. The `details` section contains `retrieved`, `schema`, and `error` keys, which have the resource as retrieved, the schema it should conform to, and a description of how it fails to conform to that schema as the respective values.

## Not found errors

Any endpoint where a resource identifier is supplied can produce a `404 Not Found Error` response if a resource with that identifier could not be found.

All not found error responses have the same form. The `kind` is "not-found", the `msg` is "The resource could not be found.", and the `details` key contains the URI of the request that resulted in this response.

