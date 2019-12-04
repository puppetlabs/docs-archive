---
layout: default
title: "Token endpoints: API v1"
canonical: "/pe/latest/rbac_token_v1.html"
---

## Authentication tokens

A user's access to PE services can be controlled using authentication tokens. Users can generate their own authentication tokens using the `token` endpoint.

### POST /auth/token

Generate an access token for the user whose login information is POSTed. This token can then be used to authenticate requests to PE services using either the `X-Authentication` header or the `token` query parameter. For more information about generating and using authentication tokens to access PE services, see the [token-based authentication](./rbac_token_auth.html) documentation.

> **Note:** This route is intended to require zero authentication. While HTTPS is still required (unless PE is explicitly configured to permit HTTP), neither a whitelisted cert nor a session cookie is needed to POST to this endpoint.

#### Request format

`login`: The user's login for the PE console (required).

`password`: The user's password for the PE console (required). 

`lifetime`: You can specify the expiration time for the token you are presently generating. This key is optional. 

When setting a token's lifetime, specify a numeric value followed by "y" (years), "d" (days), "h" (hours), "m" (minutes), or "s"(seconds). For example, a value of "12h" is 12 hours. Do not add a space between the numeric value and the unit of measurement. If you do not specify a unit, it is assumed to be seconds. If you do not want the token to expire, set the lifetime to "0". Setting it to zero gives the token a lifetime of approximately 10 years.

> **Note:** The default lifetime for all tokens is also configurable. For information on changing the default lifetime, see [Changing the default lifetime](./rbac_token_auth.html#changing-the-default-lifetime).

`label`: Assigns a user-defined label to the token you are presently generating. This key is optional. 

`description`: Add additional metadata about the requested token.

`client`: Add additional metadata about the client making the token request. 

This key will set a label for the token that can be used with other RBAC token endpoints. Labels:

* Must be no longer than 200 characters.
* Must not contain commas.
* Must contain something other than whitespace. (Whitespace will be trimmed from the beginning and end of the label, though it is allowed elsewhere.)
* Must not be the same as a label for another token for the same user.

Token labels are assigned on a per-user basis: two users can each have a token labelled "my token", but a single user cannot have two tokens both labelled "my token". You cannot use labels to refer to other users' tokens. 

#### Example requests

``` json
{"login": "jeanjackson@example.com",
 "password": "1234",
 "lifetime": "4m",
 "label": "personal workstation token"}
```

```
curl –cacert $(puppet config print cacert) -X POST -H 'Content-Type: application/json' -d '{"login": "<LOGIN>", "password": "<PASSWORD>", "lifetime": "4h", "label": "four-hour token"}' https://<HOSTNAME>:4433/rbac-api/v1/auth/token
```

The parts of this curl command request are explained below:
   
* `–cacert [FILE]`: Specifies a CA certificate as described in [Forming requests for the node classifier](./nc_forming_requests.html#authentication). Alternatively, you could use the `-k` flag to turn off SSL verification of the RBAC server so that you can use the HTTPS protocol without providing a CA cert. If you do not provide one of these options in your cURL request, cURL complains about not being able to verify the RBAC server.
   
   > **Note:** The `-k` flag is shown as an example only. You should use your own discretion when choosing the appropriate server verification method for the tool that you are using. 
   
* `-X POST`: This is an HTTP POST request to provide your login information to the RBAC service.
* `-H 'Content-Type: application/json'`: sets the `Content-Type` header to `application/json`, which indicates to RBAC that the data being sent is in JSON format..
* `-d '{"login": "<YOUR PE USER NAME>", "password": "<YOUR PE PASSWORD>", "lifetime": "4m", "label": "four-minute token"}'`: Provide the user name and password that you use to log in to the PE console. Optionally, set the token's lifetime and label. 
* `https://<HOSTNAME>:<PORT>/rbac-api/v1/auth/token`: Sends the request to the `token` endpoint. For `HOSTNAME`, provide the FQDN of the server that is hosting the PE console service. If you are making the call from the console server, you can use "localhost." For `PORT`, provide the port that the PE services (node classifier service, RBAC service, and activity service) listen on. The default port is 4433.
   
#### Return format

* **200 OK** if the credentials are good and the user is not revoked.
* **401 Unauthenticated** if the credentials are bad or the user is revoked.
* **400 Malformed** if something is wrong with the request body.

#### Example return

    {"token": "asd0u0=2jdijasodj-w0duwdhjashd,kjsahdasoi0d9hw0hduashd0a9wdy0whdkaudhaksdhc9chakdh92..."}







