---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Tokens endpoints

A user’s access to PE services can be controlled using authentication tokens. Users can revoke authentication tokens using the `tokens` endpoints.

**Related information**  


[Token-based authentication](rbac_token_auth_intro.md#)

## DELETE /tokens

Use this endpoint to revoke one or more authentication tokens, ensuring the tokens can no longer be used with RBAC.

### Parameters

The tokens must be specified using at least one of the following parameters:

|Parameter|Value|
|---------|-----|
|`revoke_tokens`|A list of complete authentication tokens to be revoked.|
|`revoke_tokens_by_usernames`|A list of usernames whose tokens are to be revoked.|
|`revoke_tokens_by_labels`|A list of the labels of tokens to revoke \(only tokens owned by the user making the request can be revoked in this manner\).|

You can supply parameters either as query parameters or as JSON-encoded in the body. If you include them as query parameters, separate the tokens, labels, or usernames by commas:

```
/tokens?revoke_tokens_by_usernames=<USER NAME>,<USER NAME>

```

If you encoded them in the body, supply them as a JSON array:

```
{"revoke_tokens_by_usernames": ["<USER NAME>", "<USER NAME>"]}

```

If you provide a parameter as both a query parameter and in the JSON body, the values from the two sources are combined. It is not an error to specify the same token using multiple means \(such as by providing the entire token to the `revoke_tokens` parameter and also including its label in the value of `revoke_tokens_by_labels`\).

In the case of an error, malformed or otherwise input, or bad request data, the endpoint still attempts to revoke as many tokens as possible. This means it's possible to encounter multiple error conditions in a single request, such as if there were malformed usernames supplied in the request *and* a database error occurred when trying to revoke the well-formed usernames.

All operations on this endpoint are idempotent—it is not an error to revoke the same token two or more times.

Any user may revoke any token by supplying the complete token in the `revoke_tokens` query parameter or request body field.

To revoke tokens by username, the user making the request must have the "Users Revoke" permission for that user.

### Example JSON body

The following is an example JSON body using each of the available parameters.

```json
{"revoke_tokens": ["eyJhbGciOiJSUzUxMiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJhZG1pbiIsImlhdCI6MTQzOTQ5Mzg0NiwiZXhwIjoxNDM5NDk0MTQ2LCJzdWIiOnsibG9naW4iOiJhZG1pbiIsImlkIjoiNDJiZjM1MWMtZjllYy00MGFmLTg0YWQtZTk3NmZlYzdmNGJkIn19.gCXZVMJ1TwRKMKCV3OESK-tMGtAALp_vBONSQQ2zU5ihWfsDU4mmmefbelR1CzA2TWrsv62DpJhtSc3iMLsSyjcUjsP6I87kjw3XFtjcB79kx5G0olhReEzkZ9wsvoR3Ha55VD2LEhfoUl8Q0jqlGRCnWUsbUeORYLkBpWpyOkbgww1UZXBLCcdMIxVcxa4xfDdGCcRT_dIB7tEtkHWH_DgA2Hbc3AMhE_9S1T_W563y9ObD58lPgFNMFr2qkFjVLQpDkKnQF1AaCDHIj-aODfSvzf9l35IE7vi97s-9fFcHUFPzboyz60GDyRJVSOwohTWpNJMlAfx0UBitgqCYXnE4rr8wKBoeOXoeEQezJZ1MJlQdi9cMzqB7t2i02uHJWmnVKio4WM1BukikjXtftnLIcJiQwpnENnXS9WmaIhpFTuKqeXRJbRN979HTEmGTArdQMpllE-u8-q0TqeN3ke4azDxdIqfhZ7H10-jDR0C5yeSBGWfX-0KEbp42cGz8lA6rrIHpSaajRWUg9yTHeUkT2crh6878orCLgfoBLDh-rOBTLeIua16sasH-ggpdHqVkTFOomEXM6UTJlp1NpuP01rNr9JMlxWhI8WpExH1l_-136D1NJm32kwo-oV6GzXRx70xq_N2CwIwObw-X1S5aUUC4KkyPtDmNvnvC1n4"]
    "revoke_tokens_by_labels": ["Workstation Token", "VPS Token"],
    "revoke_tokens_by_usernames": ["<USER NAME>", "<USER NAME>"]}
```

### Response codes

The server uses the following response codes:

|Code|Definition|
|----|----------|
|204 No Content|Sent if all operations are successful.|
|500 Application Error|Sent if there was a database error when trying to revoke tokens.|
|403|Sent if the user lacks the permission to revoke one of the supplied usernames and no database error occurred.|
|400 Malformed|Sent if one these conditions are true:

-   At least one of the tokens, usernames, or labels is malformed.

-   At least one of the usernames does not exist in the RBAC database.

-   Neither `revoke_tokens`, `revoke_tokens_by_usernames`, nor `revoke_tokens_by_labels` is supplied.

-   There are unrecognized query parameters or fields in the request body, and the user has all necessary permissions and no database error occurred.


|

All error responses follow the standard JSON error format, meaning they have `kind`, `msg`, and `details` keys.

The `kind` key is `puppetlabs.rbac/database-token-error` if the response is a 500, `permission-denied` if the response is a 403, and `malformed-token-request` if the response is a 400.

The `msg` key contains an English-language description of the problems encountered while processing the request and performing the revocations, ending with either "No tokens were revoked" or "All other tokens were successfully revoked", depending on whether any operations were successful.

The `details` key contains an object with arrays in the `malformed_tokens`, `malformed_usernames`, `malformed_labels`, `nonexistent_usernames`, `permission_denied_usernames`, and `unrecognized_parameters` fields, as well as the Boolean field `other_tokens_revoked`.

The arrays all contain bad input from the request, and the `other_tokens_revoked` field's value indicates whether any of the revocations specified in the request were successful or not.

### Example error body

The server returns an error body resembling the following:

```json
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

## DELETE /tokens/<token\>

Use this endpoint to revoke a single token, ensuring that it can no longer be used with RBAC. Authentication is required.

This endpoint is equivalent to DELETE `/tokens?revoke_tokens={TOKEN}`.

This API may be used only by the admin or API user.

### Response codes

The server uses the following response codes:

|Code|Definition|
|----|----------|
|204 No Content|Sent if all operations are successful.|
|400 Malformed|Sent if the token is malformed.|

The error response is identical to DELETE `/tokens`.

### Example error body

The error response from this endpoint is identical to DELETE `/tokens`.

```json
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

## POST /auth/token/authenticate

Use this endpoint to exchange a token for a map representing an RBAC subject and associated token data. This endpoint does not require authentication.

This endpoint accepts a JSON body containing entries for `token` and `update_last_activity?`. The `token` field is a string containing an authentication token. The `update_last_activity?` field is a Boolean data type that indicates whether a successful authentication should update the `last_active` timestamp for the token.

### Response codes

The server uses the following response codes:

|Code|Definition|
|----|----------|
|200 OK|The subject represented by the token.|
|400 invalid-token|The provided token was either tampered with or could not be parsed.|
|403 token-revoked|The provided token has been revoked.|
|403 token-expired|The token has expired and is no longer valid.|
|403 token-timed-out|The token has timed out due to inactivity.|

### Example JSON body

```
{
  "token": "0VZZ6geJQK8zJGKxBdq1atTsMLAsfCQFJuRwxsMNgCr4",
  "update_last_activity?": false
}

```

### Example return

```json
    {"description":null,
     "creation":"YYYY-MM-DDT22:24:30Z",
     "email":"franz@kafka.com",
     "is_revoked":false,
     "last_active":"YYYY-MM-DDT22:24:31Z",
     "last_login":"YYYY-MM-DDT22:24:31.340Z",
     "expiration":"YYYY-MM-DDT22:29:30Z",
     "is_remote":false,
     "client":null,
     "login":"franz@kafka.com",
     "is_superuser":false,
     "label":null,
     "id":"c84bae61-f668-4a18-9a4a-5e33a97b716c",
     "role_ids":[1, 2, 3],
     "user_id":"c84bae61-f668-4a18-9a4a-5e33a97b716c",
     "timeout":null,
     "display_name":"Franz Kafka",
     "is_group":false}
```

