# Directory service endpoints

Use the `ds` \(directory service\) API endpoints to get information about the directory service, test your directory service connection, and replace directory service connection settings.

To connect to the directory service anonymously, specify `null` for the lookup user and lookup password or leave these fields blank.

**Related information**  


[External directory settings](rbac_ldap_intro.md#)

## GET /ds

Get the connected directory service information. Authentication is required.

### Return format

Returns a 200 OK response with an object representing the connection.

For example:

```
{"display_name": "AD",
"hostname": "ds.foobar.com",
"port": 10379, ...}

```

If the connection settings have not been specified, returns a 200 OK response with an empty JSON object.

For example:

```
{ }
```

## GET /ds/test

Runs a connection test for the connected directory service. Authentication is required.

### Return format

If the connection test is successful, returns a 200 OK response with information about the test run.

For example:

```
{"elapsed": 10}
```

### Error responses

-   400 Bad Request if the request is malformed.
-   401 Unauthorized if no user is logged in.
-   403 Forbidden if the current user lacks the permissions to test the directory settings.

```
{"elapsed": 20, "error": "..."} 
```

## PUT /ds/test

Performs a connection test with the submitted settings. Authentication is required.

### Request format

Accepts the full set of directory settings keys with values defined.

### Return format

If the connection test is successful, returns information about the test run.

For example:

```
{"elapsed": 10}
```

### Error responses

If the request times out or encounters an error, returns information about the test run.

For example:

```
{"elapsed": 20, "error": "..."} 
```

## PUT /ds

Replaces current directory service connection settings. Authentication is required.

When changing directory service settings, you must specify all of the required directory service settings in the PUT request, including the required settings that are remaining the same. You do not need to specify optional settings unless you are changing them.

### Request format

Accepts directory service connection settings. To "disconnect" the DS, `PUT` either an empty object \("\{\}"\) or the settings structure with all values set to null.

For example:

```
{"hostname": "ds.somehost.com",
"name"
"port": 10389,
"login": "frances", ...}
```

### Working with nested groups

When authorizing users, the RBAC service has the ability to search nested groups. Nested groups are groups that are members of external directory groups. For example, if your external directory has a "System Administrators" group, and you've given that group the "Superusers" user role in RBAC, then RBAC also searches any groups that are members of the "System Administrators" group and assign the "Superusers" role to users in those member groups.

By default, RBAC does not search nested groups. To enable nested group searches, specify the  `search_nested_groups` field and give it a value of `true`.

**Note:** In PE 2015.3 and earlier versions, RBAC's default was to search nested groups. When **upgrading** from one of these earlier versions, this default setting is preserved and RBAC continues to search nested groups. If you have a very large number of nested groups, you might experience a slowdown in performance when users are logging in because RBAC is searching the entire hierarchy of groups. To avoid performance issues, change the `search_nested_groups` field to `false` for a more shallow search in which RBAC only searches the groups it has been configured to use for user roles.

### Using StartTLS connections

To use a StartTLS connection, specify `"start_tls": true`. When set to `true`, StartTLS is used to secure your connection to the directory service, and any certificates that you have configured through the DS trust chain setting will be used to verify the identity of the directory service. When specifying StartTLS, make sure that you don't also have SSL set to true.

### Disabling matching rule in chain

When PE detects an Active Directory that supports the LDAP\_MATCHING\_RULE\_IN\_CHAIN feature, it automatically uses it. Under some specific circumstances, you may need to disable this setting. To disable it, specify `"disable_ldap_matching_rule_in_chain": true` in the `PUT` request. This is an optional setting.

### Return format

Returns a 200 OK response with an object showing the updated connection settings.

For example:

```
{"hostname": "ds.somehost.com",
"port": 10389,
"login": "frances", ...}
```

