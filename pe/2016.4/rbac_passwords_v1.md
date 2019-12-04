---
layout: default
title: "Password endpoints: API v1"
canonical: "/pe/latest/rbac_passwords_v1.html"
---

When local users forget passwords or lock themselves out of PE by attempting to log in with incorrect credentials too many times, you'll have to generate a password reset token for them. The Password APIs enable you to generate password reset tokens for a specific local user or with a token that contains a temporary password in the body.

>**Tip:** The PE console admin password can also be [reset using a password reset script](./rbac_user_roles.html#reset-the-admin-password) available on the PE console node.

>**Note:** 10 is the default number of login attempts that can be made with incorrect credentials before a user is locked out. You can change the value with the [`failed-attempts-lockout`](./rbac_config_v1.html#failed-attempts-lockout).

The path for RBAC requests is as follows, where localhost:4433 are the machine and port if you're making the call from the console machine. If you're not making the call from the console machine, then use the console machine's hostname in place of localhost.

    https://localhost:4433/rbac-api/v1/

### POST /users/:sid/password/reset

Generates a password reset token for the specified local user. The generated token
is a single use, so it can only be used to reset the password
once, and has a limited lifetime. The lifetime is based on a configuration
value. The token is generated using a random token generation algorithm that's non-numeric and difficult to predict. This token is to be given to the appropriate
user for use with the `/auth/reset` endpoint. Authentication is
required.

**Returns:**

* **200 OK** With the token in the body of the response.
* **403 Forbidden** If the user does not have permissions to create a reset path
for the user, or if the user is a remote user.
* **404 Not found** If a user with the given identifier does not exist.

**Example return:**

    eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiO...

### POST /auth/reset

Resets a local user's password using a one-time token, with the password in the
body. The token should be obtained via the `/users/:sid/password/reset` endpoint.
The appropriate user is identified in the payload of the token. This
does not establish a valid logged in session for the user. No authentication is required to use this endpoint.

**Example body:**

    {"token": "text of token goes here",
     "password":"someotherpassword"}

**Returns:**

* **200 OK** If the token is valid and the password has been changed.
* **403 Permission denied** If the token has been used or is invalid.

### PUT /users/current/password

Changes the password for the current local user. A payload containing the current
password must be provided. Authentication is required.

**Example body:**

    {"current_password": "somepassword",
     "password": "someotherpassword"}

**Returns:**

* **200 OK** If the password is changed successfully
* **403 Forbidden** If the user is a remote user, or if the `current_password`
doesn't match the current password stored for the user. The body of the
response should include a message that specifies the cause of the failure.