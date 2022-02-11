# Users endpoints

RBAC enables you to manage local users as well as those who are created remotely, on a directory service. With the `users` endpoints, you can get lists of users and create new local users.

## Users keys

The following keys are used with the RBAC v1 API's `users` endpoints.

|Key|Explanation|Example|
|---|-----------|-------|
|`id`|A UUID string identifying the user.|`"4fee7450-54c7-11e4-916c-0800200c9a66"`|
|`login`|A string used by the user to log in. Must be unique among users and groups.|`"admin"`|
|`email`|An email address string. Not currently utilized by any code in PE.|`"hill@example.com"`|
|`display_name`|The user's name as a string.|`"Kalo Hill"`|
|`role_ids`|An array of role IDs indicating which roles should be directly assigned to the user. An empty array is valid.|`[3 6 5]`|
|`is_group`

`is_remote`

`is_superuser`

|These flags indicate the type of user. `is_group` should always be false for a user.|`true`/`false`|
|`is_revoked`|Setting this flag to `true` prevents the user from accessing any routes until the flag is unset or the user's password is reset via token.|`true`/`false`|
|`last_login`|This is a timestamp in UTC-based ISO-8601 format \(YYYY-MM-DDThh:mm:ssZ\) indicating when the user last logged in. If the user has never logged in, this value is null.|`"2014-05-04T02:32:00Z"`|
|`inherited_role_ids`

\(remote users only\)

|An array of role IDs indicating which roles a remote user inherits from their groups.|`[9 1 3]`|
|`group_ids`

\(remote users only\)

|An array of UUIDs indicating which groups a remote user inherits roles from.|`["3a96d280-54c9-11e4-916c-0800200c9a66"]`|

## GET /users

Fetches all users, both local and remote \(including the superuser\). Supports filtering by ID through query parameters. Authentication is required.

### Request format

To request all the users:

```
GET /users
```

To request specific users, add a comma-separated list of user IDs:

```
GET /users?id=fe62d770-5886-11e4-8ed6-0800200c9a66,1cadd0e0-5887-11e4-8ed6-0800200c9a66
```

### Response format

The response is a JSON object that lists the metadata for all requested users.

For example:

```json
[{
  "id": "fe62d770-5886-11e4-8ed6-0800200c9a66",
  "login": "Kalo",
  "email": "kalohill@example.com",
  "display_name": "Kalo Hill",
  "role_ids": [1,2,3...],
  "is_group" : false,
  "is_remote" : false,
  "is_superuser" : true,
  "is_revoked": false,
  "last_login": "2014-05-04T02:32:00Z"
},{
  "id": "07d9c8e0-5887-11e4-8ed6-0800200c9a66",
  "login": "Jean",
  "email": "jeanjackson@example.com",
  "display_name": "Jean Jackson",
  "role_ids": [2, 3],
  "inherited_role_ids": [5],
  "is_group" : false,
  "is_remote" : true,
  "is_superuser" : false,
  "group_ids": ["2ca57e30-5887-11e4-8ed6-0800200c9a66"],
  "is_revoked": false,
  "last_login": "2014-05-04T02:32:00Z"
},{
  "id": "1cadd0e0-5887-11e4-8ed6-0800200c9a66",
  "login": "Amari",
  "email": "amariperez@example.com",
  "display_name": "Amari Perez",
  "role_ids": [2, 3],
  "inherited_role_ids": [5],
  "is_group" : false,
  "is_remote" : true,
  "is_superuser" : false,
  "group_ids": ["2ca57e30-5887-11e4-8ed6-0800200c9a66"],
  "is_revoked": false,
  "last_login": "2014-05-04T02:32:00Z"
}]
```

## GET /users/<sid\>

Fetches a single user by its subject ID \(sid\). Authentication is required.

### Response format

For all users, the user contains an ID, a login, an email, a display name, a list of role-ids the user is directly assigned to, and the last login time in UTC-based ISO-8601 format \(YYYY-MM-DDThh:mm:ssZ\), or null if the user has not logged in yet. It also contains an "is\_revoked" field, which, when set to true, prevents a user from authenticating.

For example:

```json
{"id": "fe62d770-5886-11e4-8ed6-0800200c9a66",
"login": "Amari",
"email": "amariperez@example.com",
"display_name": "Amari Perez",
"role_ids": [1,2,3...],
"is_group" : false,
"is_remote" : false,
"is_superuser" : false,
"is_revoked": false,
"last_login": "2014-05-04T02:32:00Z"}
```

For remote users, the response additionally contains a field indicating the IDs of the groups the user inherits roles from and the list of inherited role IDs.

For example:

```json
{"id": "07d9c8e0-5887-11e4-8ed6-0800200c9a66",
"login": "Jean",
"email": "jeanjackson@example.com",
"display_name": "Jean Jackson",
"role_ids": [2,3...],
"inherited_role_ids": [],
"is_group" : false,
"is_remote" : true,
"is_superuser" : false,
"group_ids": ["b28b8790-5889-11e4-8ed6-0800200c9a66"],
"is_revoked": false,
"last_login": "2014-05-04T02:32:00Z"}
```

## GET /users/current

Fetches the data about the current authenticated user, with the exact same behavior as `GET /users/<sid>`, except that `<sid>` is assumed from the authentication context. Authentication is required.

## POST /users

Creates a new local user. You can add the new user to user roles by specifying an array of roles in `role_ids`. You can set a password for the user in `password`. For the password to work in the PE console, it needs to be a minimum of six characters. Authentication is required.

### Request format

Accepts a JSON body containing entries for `email`, `display_name`, `login`, `role_ids`, and `password`. The password field is optional. The created account is not useful until the password is set.

For example:

```json
{"login":"Kalo",
"email":"kalohill@example.com",
"display_name":"Kalo Hill",
"role_ids": [1,2,3],
"password": "yabbadabba"}
```

### Response format

If the create operation is successful, a 201 Created response with a location header that points to the new resource will be returned.

### Error responses

If the email or login for the user conflicts with an existing user's login, a 409 Conflict response will be returned.

## PUT /users/<sid\>

Replaces the user with the specified ID \(sid\) with a new user object. Authentication is required.

### Request format

Accepts an updated user object with all keys provided when the object is received from the API. The behavior varies based on user type. All types have a `role_id` array that indicates all the roles the user should belong to. An empty roles array removes all roles directly assigned to the group.

The below examples show what keys must be submitted. Keys marked with asterisks are the only ones that can be changed via the API.

An example for a local user:

```json
{"id": "c8b2c380-5889-11e4-8ed6-0800200c9a66",
**"login": "Amari",**
**"email": "amariperez@example.com",**
**"display_name": "Amari Perez",**
**"role_ids": [1, 2, 3],**
"is_group" : false,
"is_remote" : false,
"is_superuser" : false,
**"is_revoked": false,**
"last_login": "2014-05-04T02:32:00Z"}
```

An example for a remote user:

```json
{"id": "3271fde0-588a-11e4-8ed6-0800200c9a66",
"login": "Jean",
"email": "jeanjackson@example.com",
"display_name": "Jean Jackson",
**"role_ids": [4, 1],**
"inherited_role_ids": [],
"group_ids: [],
"is_group" : false,
"is_remote" : true,
"is_superuser" : false,
**"is_revoked": false,**
"last_login": "2014-05-04T02:32:00Z"}
```

### Response format

The request returns a 200 OK response, along with the user object with the changes made.

### Error responses

If the login for the user conflicts with an existing user login, a 409 Conflict response will be returned.

## DELETE /users/<sid\>

Deletes the user with the specified ID \(sid\), regardless of whether they are a user defined in RBAC or a user defined by a directory service. In the case of directory service users, while this action removes a user from the console, that user is still able to log in \(at which point they are re-added to the console\) if they are not revoked. Authentication is required.

**Remember:** The `admin` user and the `api_user` cannot be deleted.

### Request format

For example, to delete a user with the ID 3982a629-1278-4e38-883e-12a7cac91535 by using a curl command:

```
curl -X DELETE \
 --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem \
 --cert /opt/puppet/share/puppet-dashboard/certs/pe-internal-dashboard.cert.pem \
 --key /opt/puppet/share/puppet-dashboard/certs/pe-internal-dashboard.private_key.pem \
 https://$(hostname -f):4433/rbac-api/v1/users/3982a629-1278-4e38-883e-12a7cac91535

```

### Response format

If the user is successfully deleted, a 204 No Content response with an empty body will be returned.

### Error responses

If the current user does not have the `users:edit` permission for this user group, a 403 Forbidden response will be returned.

If a user with the provided identifier does not exist, a 404 Not Found response will be returned.

