# Token endpoints

A user's access to PE services can be controlled using authentication tokens. Users can generate their own authentication tokens using the `token` endpoint.

**Related topics**  


[Token-based authentication](rbac_token_auth_intro.md#)

## Token keys

The following keys are used with the `token` endpoint.

|Key|Explanation|
|---|-----------|
|`login`|The user's login for the PE console \(required\).|
|`password`|The user's password for the PE console \(required\).|
|`lifetime`|The length of time the token will be active before expiration \(optional\).|
|`description`|Additional metadata about the requested token \(optional\).|
|`client`|Additional metadata about the client making the token request \(optional\).|
|`label`|A user-defined label for the token \(optional\).|

### The lifetime key

When setting a token's lifetime, specify a numeric value followed by `y` \(years\), `d` \(days\), `h` \(hours\), `m` \(minutes\), or `s` \(seconds\). For example, a value of `12h` is 12 hours. Do not add a space between the numeric value and the unit of measurement. If you do not specify a unit, it is assumed to be seconds. If you do not want the token to expire, set the lifetime to `0`. Setting it to zero gives the token a lifetime of approximately 10 years.

**Tip:** The default lifetime for all tokens is also configurable. See [Change the token's default lifetime](rbac_token_auth_intro.md#) for configuration instructions.

### The label key

You can choose to select a label for the token that can be used with other RBAC token endpoints. Labels:

-   Must be no longer than 200 characters.
-   Must not contain commas.
-   Must contain something other than whitespace. \(Whitespace will be trimmed from the beginning and end of the label, though it is allowed elsewhere.\)
-   Must not be the same as a label for another token for the same user.

Token labels are assigned on a per-user basis: two users can each have a token labelled `my token`, but a single user cannot have two tokens both labelled `my token`. You cannot use labels to refer to other users' tokens.

## POST /auth/token

Generates an access token for the user whose login information is POSTed. This token can then be used to authenticate requests to PE services using either the `X-Authentication` header or the `token` query parameter.

This route is intended to require zero authentication. While HTTPS is still required \(unless PE is explicitly configured to permit HTTP\), neither a whitelisted cert nor a session cookie is needed to POST to this endpoint.

### Request format

Accepts a JSON object or curl command with the user's login and password information. The token's lifetime, a user-specified label, and additional metadata may be added, but are not required.

An example JSON request:

```json
{"login": "jeanjackson@example.com",
 "password": "1234",
 "lifetime": "4m",
 "label": "personal workstation token"}
```

An example curl command request:

```
curl –cacert $(puppet config print cacert) -X POST -H 'Content-Type: application/json' -d '{"login": "<LOGIN>", "password": "<PASSWORD>", "lifetime": "4h", "label": "four-hour token"}' https://<HOSTNAME>:4433/rbac-api/v1/auth/token
```

The various parts of this curl command request are explained as follows:

-   `–cacert [FILE]`: Specifies a CA certificate as described in Forming requests for the node classifier. Alternatively, you could use the `-k` flag to turn off SSL verification of the RBAC server so that you can use the HTTPS protocol without providing a CA cert. If you do not provide one of these options in your cURL request, cURL complains about not being able to verify the RBAC server.

    **Note:** The `-k` flag is shown as an example only. You should use your own discretion when choosing the appropriate server verification method for the tool that you are using.


-   `-X POST`: This is an HTTP POST request to provide your login information to the RBAC service.
-   `-H 'Content-Type: application/json'`: sets the `Content-Type` header to `application/json`, which indicates to RBAC that the data being sent is in JSON format..
-   `-d '{"login": "<YOUR PE USER NAME>", "password": "<YOUR PE PASSWORD>", "lifetime": "4m", "label": "four-minute token"}'`: Provide the user name and password that you use to log in to the PE console. Optionally, set the token's lifetime and label.
-   `https://<HOSTNAME>:<PORT>/rbac-api/v1/auth/token`: Sends the request to the `token` endpoint. For `HOSTNAME`, provide the FQDN of the server that is hosting the PE console service. If you are making the call from the console server, you can use "localhost." For `PORT`, provide the port that the PE services \(node classifier service, RBAC service, and activity service\) listen on. The default port is 4433.

### Response format

Returns a 200 OK response if the credentials are good and the user is not revoked, along with a token.

For example:

```
{"token": "asd0u0=2jdijasodj-w0duwdhjashd,kjsahdasoi0d9hw0hduashd0a9wdy0whdkaudhaksdhc9chakdh92..."}
```

### Error responses

Returns a 401 Unauthenticated response if the credentials are bad or the user is revoked.

Returns a 400 Malformed response if something is wrong with the request body.

