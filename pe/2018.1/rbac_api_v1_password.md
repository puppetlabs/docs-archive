# Password endpoints

When local users forget passwords or lock themselves out of PE by attempting to log in with incorrect credentials too many times, you'll have to generate a password reset token for them. The `password` endpoints enable you to generate password reset tokens for a specific local user or with a token that contains a temporary password in the body.

**Tip:** The PE console admin password can also be reset using a password reset script available on the PE console node.

**Tip:** 10 is the default number of login attempts that can be made with incorrect credentials before a user is locked out. You can change the value by configuring the `failed-attempts-lockout` parameter.

**Related information**  


[Reset the admin password](console_accessing.md#)

## POST /users/:sid/password/reset

Generates a single-use password reset token for the specified local user.

The generated token can only be used to reset the password once and it has a limited lifetime. The lifetime is based on a configuration value `puppet_enterprise::profile::console::rbac_password_reset_expiration` \(number of hours\). The default value is 24 hours. Authentication is required.

This token is to be given to the appropriate user for use with the `/auth/reset` endpoint.

### Response format

Returns a 201 Created response. For example:

```no-highlight
eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJhcGlfdXNlciIsInN1YiI6ImE3YzA4MTY3LWE1MDUtNDBlMy05NWQxLWQzMTE4NDQwM2Q5ZiIsImV4cCI6MTU0ODg4NTU0OCwiaWF0IjoxNTQ4Nzk5MTQ4fQ.qOG0w5UuZiBrT8yPoTG3YHwOhhkchrUh7kvFNiBFw0gPyfyZ2yhfjHcffJ-p0aUcdmBtuD95J9NGEAPtTwsDcux0_33hPz03m0Rp3hDSjb1QwyTBTuf2rpqymaYo1VRw-Eou60dUiqBAAqkQ9rMtotRtO6YTnQ8M6p9RxiFk_u5YjZDrhLZSqYxg-LY-cY2IFow_XFIwc9vwWBuDX1LwLB9TqQJoI2NVNBymoHABzoKrnV-dKGFRawF-gBTwdtvS-oDFSxlkDBwkangGq2jb2Ghszhb8jdK9wWQ_8fnOcUk_kLlbE7Wr0htK045tT9BPazwZaZWagHiojI_YFyJfBiB_dBKAFkU4w5VAPnzBdRLtp8rjFe_B_HLe8Halsk_BTOB-F3XquRE-4C2EbejQLd_u-WZGnJpZV_HGK4Spd56V-anuDq1AakaW3IzDJkJuPI4CxUE8_xpfFFLRdofTHrLPpwo5dszbKK-9xw3JfIMhnsK4e2H_5Nywk0w95lvonKuYp9IFj85CZH_k5tDcUQN8_B3p8DONWo_Y2Xh6kv210qWYmXFh2kn8zVTuKT19EyIPoPHcJwyA3Y569UXgkVUB9LAcwyEnEbU70kKXwlXZUfsQOzMIymkIR4kVqBxDgwnrABMEcTFK24_rkg8srKWP6f_TcEg3588C3_DTR8g

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

