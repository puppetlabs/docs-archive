# RBAC service errors

You’re likely to encounter some errors when using the RBAC API. You’ll want to familiarize yourself with the error response descriptions and the general error responses.

## Error response format

When the client specifies an `accept` header in the request with type `application/json`, the RBAC service returns errors in a standard format.

Each response is an object containing the following keys:

|Key|Definition|
|---|----------|
|`kind`|A string classifying the error. It is be the same for all errors that have the same type of information in their `details` key.|
|`msg`|A human-readable message describing the error.|
|`details`|Additional machine-readable information about the error condition. The format of this key's value varies between kinds of errors, but is the same for each kind of error.|

When returning errors in `text/html`, the body is the contents of the `msg` field.

## General error responses

Any endpoint accepting a JSON body can return several kinds of 400 Bad Request responses.

|Response|Status|Description|
|--------|------|-----------|
|`malformed-request`|400|The submitted data is not valid JSON. The `details` key consists of one field, `error`, which contains the error message from the JSON parser.|
|`schema-violation`|400|The submitted data has an unexpected structure, such as invalid fields or missing required fields. The `msg` contains a description of the problem. The `details` are an object with three keys:

-   `submitted`: The submitted data as it was seen during schema validation.

-   `schema`: The expected structure of the data.

-   `error`: A structured description of the error.


|
|`inconsistent-id`|400|Data was submitted to an endpoint where the ID of the object is a part of the URL and the submitted data contains an `id` field with a different value. The `details` key consists of two fields, `url-id` and `body-id`, showing the IDs from both sources.|
|`invalid-id-filter`|400|A URL contains a filter on the ID with an invalid format. No details are given with this error.|
|`invalid-uuid`|400|An invalid UUID was submitted. No details are given with this error.|
|`user-unauthenticated`|401|An unauthenticated user attempted to access a route that requires authentication.|
|`user-revoked`|401|A user who has been revoked attempted to access a route that requires authentication.|
|`api-user-login`|401|A person attempted to log in as the api\_user with a password \(api\_user does not support username/password authentication\).|
|`remote-user-conflict`|401|A remote user who is not yet known to RBAC attempted to authenticate, but a local user with that login already exists.

The solution is to change either the local user's login in RBAC, or to change the remote user's login, either by changing the `user_lookup_attr`in the DS settings or by changing the value in the directory service itself.

|
|`permission-denied`|403|A user attempted an action that they are not permitted to perform.|
|`admin-user-immutable``admin-user-not-in-admin-role`

`default-roles-immutable`

|403|A user attempted to edit metadata or associations belonging to the default roles \("Administrators", "Operators", "Code Deployers", or "Viewers"\) or default users \("admin" or "api\_user"\) that they are not allowed to change.|
|`conflict`|409|A value for a field that is supposed to be unique was submitted to the service and another object has that value. For example, when a user is created with the same login as an existing user.|
|`invalid-associated-id`|422|An object was submitted with a list of associated IDs \(for example, `user_ids`\) and one or more of those IDs does not correspond to an object of the correct type.|
|`no-such-user-LDAP`

`no-such-group-LDAP`

|422|An object was submitted with a list of associated IDs \(for example, `user_ids`\) and one or more of those IDs does not correspond to an object of the correct type.|
|`non-unique-lookup-attr`|422|A login was attempted but multiple users are found via LDAP for the given username. The directory service settings must use a `user_lookup_attr` that is guaranteed to be unique within the provided user's RDN.|
|`server-error`|500|Occurs when the server throws an unspecified exception. A message and stack trace is available in the logs.|

