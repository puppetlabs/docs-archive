# Password endpoints

When local users forget passwords or lock themselves out of PE by attempting to log in with incorrect credentials too many times, you'll have to generate a password reset token for them. The `password` endpoints enable you to generate password reset tokens for a specific local user or with a token that contains a temporary password in the body.

**Tip:** The PE console admin password can also be reset using a password reset script available on the PE console node.

**Tip:** 10 is the default number of login attempts that can be made with incorrect credentials before a user is locked out. You can change the value by configuring the `failed-attempts-lockout` parameter.

**Related topics**  


[Reset the admin password](console_accessing.md#)

## POST /users/:sid/password/reset

Generates a single-use password reset token for the specified local user. The generated token can only be used to reset the password once, and has a limited lifetime. The lifetime is based on a configuration value. Authentication is required.

This token is to be given to the appropriate user for use with the `/auth/reset` endpoint.

### Response format

Returns a 200 OK response with the password reset token in the body of the response.

For example:

```
eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiO...
```

### Error responses

Returns a 403 Forbidden response if the user does not have permissions to create a reset path for the specified user, or if the user is a remote user.

Returns a 404 Not Found response if a user with the given identifier does not exist.

## POST /auth/reset

Resets a local user's password using a one-time token obtained via the `/users/:sid/password/reset` endpoint, with the new password in the body. No authentication is required to use this endpoint.

### Request format

The appropriate user is identified in the payload of the token. This endpoint does not establish a valid logged-in session for the user.

For example:

```
{"token": "text of token goes here",
 "password":"someotherpassword"}
```

### Response format

Returns a 200 OK response if the token is valid and the password has been successfully changed.

### Error responses

Returns a 403 Permission Denied response if the token has already been used or is invalid.

## PUT /users/current/password

Changes the password for the current local user. A payload containing the current password must be provided. Authentication is required.

### Request format

The current and new passwords must both be included.

For example:

```
{"current_password": "somepassword",
 "password": "someotherpassword"}
```

### Response format

Returns a 200 OK response if the password has been successfully changed.

### Error responses

Returns a 403 Forbidden response if the user is a remote user, or if `current_password` doesn't match the current password stored for the user. The body of the response will include a message that specifies the cause of the failure.

