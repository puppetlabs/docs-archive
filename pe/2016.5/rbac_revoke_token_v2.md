---
layout: default
title: "Revoke token endpoints: API v2"
canonical: "/pe/latest/rbac_revoke_token_v2.html"
---

## Revoke authentication tokens

A user's access to PE services can be controlled using authentication tokens. Users can revoke authentication tokens using the `tokens` endpoint.

### DELETE /tokens

Revoke one or more authentication tokens, ensuring that they can no longer be used with RBAC. Authentication is required.

The tokens must be specified using at least one of the following parameters:

* `revoke_tokens`: a list of complete authentication tokens to be revoked.
* `revoke_tokens_by_usernames`: a list of usernames whose tokens are to be revoked.
* `revoke_tokens_by_labels`: a list of the labels of tokens to revoke (**only tokens owned by the user making the request can be revoked in this manner**).

These may be supplied either as query parameters or JSON-encoded in the body.
If they are included as query parameters, the tokens, labels, or usernames should be separated by commas: 

`/tokens?revoke_tokens_by_usernames=<USER NAME>,<USER NAME>`.

If they are encoded in the body, they should be supplied as a JSON array: 

`{"revoke_tokens_by_usernames": ["<USER NAME>", "<USER NAME>"]}`

A single request can include any combination of allowed parameters and parameter types, provided it includes at least one of `revoke_tokens`, `revoke_tokens_by_usernames`, and/or `revoke_tokens_by_labels`.

If a parameter is provided as both a query parameter and in the JSON body, the values from the two sources will be combined. It is not an error to specify the same token using multiple means (such as by providing the entire token to the `revoke_tokens` parameter and also including its label in the value of `revoke_tokens_by_labels`).

In the case of an error, malformed or otherwise input, or bad request data, the endpoint still attempts to revoke as many tokens as possible. This means it's possible to encounter multiple error conditions in a single request, such as if there were malformed usernames supplied in the request *and* a database error occurred when trying to revoke the well-formed usernames.

All operations on this endpoint are idempotent; it is not an error to revoke the same token two or more times.

**Example body:**

``` json
    {"revoke_tokens": ["eyJhbGciOiJSUzUxMiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJhZG1pbiIsImlhdCI6MTQzOTQ5Mzg0NiwiZXhwIjoxNDM5NDk0MTQ2LCJzdWIiOnsibG9naW4iOiJhZG1pbiIsImlkIjoiNDJiZjM1MWMtZjllYy00MGFmLTg0YWQtZTk3NmZlYzdmNGJkIn19.gCXZVMJ1TwRKMKCV3OESK-tMGtAALp_vBONSQQ2zU5ihWfsDU4mmmefbelR1CzA2TWrsv62DpJhtSc3iMLsSyjcUjsP6I87kjw3XFtjcB79kx5G0olhReEzkZ9wsvoR3Ha55VD2LEhfoUl8Q0jqlGRCnWUsbUeORYLkBpWpyOkbgww1UZXBLCcdMIxVcxa4xfDdGCcRT_dIB7tEtkHWH_DgA2Hbc3AMhE_9S1T_W563y9ObD58lPgFNMFr2qkFjVLQpDkKnQF1AaCDHIj-aODfSvzf9l35IE7vi97s-9fFcHUFPzboyz60GDyRJVSOwohTWpNJMlAfx0UBitgqCYXnE4rr8wKBoeOXoeEQezJZ1MJlQdi9cMzqB7t2i02uHJWmnVKio4WM1BukikjXtftnLIcJiQwpnENnXS9WmaIhpFTuKqeXRJbRN979HTEmGTArdQMpllE-u8-q0TqeN3ke4azDxdIqfhZ7H10-jDR0C5yeSBGWfX-0KEbp42cGz8lA6rrIHpSaajRWUg9yTHeUkT2crh6878orCLgfoBLDh-rOBTLeIua16sasH-ggpdHqVkTFOomEXM6UTJlp1NpuP01rNr9JMlxWhI8WpExH1l_-136D1NJm32kwo-oV6GzXRx70xq_N2CwIwObw-X1S5aUUC4KkyPtDmNvnvC1n4"]
    "revoke_tokens_by_labels": ["Workstation Token", "VPS Token"],
    "revoke_tokens_by_usernames": ["<USER NAME>", "<USER NAME>"]}
```

**Authorization:**

Any user may revoke any token by supplying the complete token in the `revoke_tokens` query parameter or request body field.

To revoke tokens by username, the user making the request must have the ["Users Revoke" permission](./rbac_permissions.html) for that user.

**Returns:**

* **204 No Content** if all operations are successful.
* **500 Application Error** if there was a database error when trying to revoke tokens.
* **403** if the user lacks the permission to revoke one of the supplied usernames and no database error occurred.
* **400 Malformed** if one these conditions are true:
    * at least one of the tokens, usernames, or labels is malformed;
    * at least one of the usernames does not exist in the RBAC database;
    * neither `revoke_tokens`, `revoke_tokens_by_usernames`, nor `revoke_tokens_by_labels` is supplied;
    * there are unrecognized query parameters or fields in the request body;
  and the user has all necessary permissions and no database error occurred.

All error responses follow the standard JSON error format, meaning they have `kind`, `msg`, and `details` keys.

The `kind` key is `puppetlabs.rbac/database-token-error` if the response is a 500, `permission-denied` if the response is a 403, and `malformed-token-request` if the response is a 400.

The `msg` key contains an English-language description of the problems encountered while processing the request and performing the revocations, ending with either "No tokens were revoked" or "All other tokens were successfully revoked", depending on whether any operations were successful.

The `details` key contains an object with arrays in the `malformed_tokens`, `malformed_usernames`, `malformed_labels`, `nonexistent_usernames`, `permission_denied_usernames`, and `unrecognized_parameters` fields, as well as the boolean field `other_tokens_revoked`.

The arrays all contain bad input from the request, and the `other_tokens_revoked` field's value indicates whether any of the revocations specified in the request were successful or not.

**Example error body:**

``` json
    {"kind": "malformed-request",
     "msg": "The following user does not exist: FormerEmployee. All other tokens were successfully revoked.",
     "details": {"malformed_tokens": [],
                 "malformed_labels": [],
                 "malformed_usernames": [],
                 "nonexistent_usernames": ["FormerEmployee"],
                 "permission_denied_usernames": [],
                 "unrecognized_parameters": [],
                 "other_tokens_revoked": true}}
```

### DELETE /tokens/&lt;token&gt;

Revoke a single token, ensuring that it can no longer be used with RBAC. Authentication is required.

Equivalent to DELETE `/tokens?revoke_tokens={TOKEN}`.

**Authorization:**

Currently, this API may only be used by the admin or API user.

**Returns:**

* **204 No Content** if all operations are successful.
* **400 Malformed** if the token is malformed.

The error response is identical to DELETE `/tokens`.

**Example error body:**

``` json
    {"kind": "malformed-request",
     "msg": "The following token is malformed: notAToken. This can be caused by an error while copying and pasting. No tokens were revoked."
     "details": {"malformed_tokens": ["notAToken"],
                 "malformed_labels": [],
                 "malformed_usernames": [],
                  "nonexistent_usernames": [],
                  "permission_denied_usernames": [],
                  "unrecognized_parameters": [],
                  "other_tokens_revoked": false}}
```

