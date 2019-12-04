---
layout: default
title: "Directory service endpoints: API v1"
canonical: "/pe/latest/rbac_dsref_v1.html"
---

RBAC enables you to connect with a directory service and work with users and groups already established on your directory service. The Directory Service APIs enable you to get information about the directory service, test your directory service connection, and replace directory service connection settings.

To connect to the directory service anonymously, specify `null` or `""` for the lookup user and lookup password.

### GET /ds
Get the connected directory service information. Authentication is required.

**Returns:**

* **200 OK** An object representing the connection. If the connection settings have not been specified, then an empty JSON object is
  returned. See [the directory settings](./rbac_ldap.html) for the definitive list of settings.

**Example return:**

        {"name": "AD",
         "hostname": "ds.foobar.com",
         "port": 10379, ...}

**Example empty return:**

      { }

### GET /ds/test
Runs a connection test for the connected directory service. Authentication is required.

**Returns:**

* **200 OK** If successful.
* **400 Bad Request** If the request is malformed.
* **401 Unauthorized** If no user is logged in.
* **403 Forbidden** If the current user lacks the permissions to test the directory settings.

In the 200 or 400 case, the body has information about the test run.

**Example returns:**

If error or timeout:

        {"elapsed": 20, "error": "..."}

If success:

        {"elapsed": 10}

### PUT /ds/test
Run a connection test with the submitted settings. Authentication is required.

**Accepts:** The full set of directory settings keys with values defined.
Refer to [the directory settings documentation](./rbac_ldap.html) for the full list of settings.

**Example returns:**

If error or timeout:

        {"elapsed": 20, "error": "..."}

If success:

        {"elapsed": 10}

### PUT /ds
Replaces current directory service connection settings. Authentication is required.

**Accepts:** Directory service connection settings. To "disconnect"
  the DS, `PUT` either an empty object ("{}") or the settings structure with all values set to null. Refer to [the directory settings documentation](./rbac_ldap.html) for the full list of settings.

#### Note: Nested groups

When authorizing users, the RBAC service has the ability to search nested groups. Nested groups are groups that are members of external directory groups. For example, if your external directory has a "System Administrators" group, and you've given that group the "Superusers" user role in RBAC, then RBAC also searches any groups that are members of the "System Administrators" group and assign the "Superusers" role to users in those member groups.

By default, RBAC does not search nested groups. To enable nested group searches, specify the `search_nested_groups` field and give it a value of `true`.

In PE 2015.3 and earlier versions, RBAC's default was to search nested groups. When **upgrading** from one of these earlier versions, this default setting is preserved and RBAC continues to search nested groups. If you have a very large number of nested groups, you might experience a slowdown in performance when users are logging in because RBAC is searching the entire hierarchy of groups. To avoid performance issues, change the `search_nested_groups` field to `false` for a more shallow search in which RBAC only searches the groups it has been configured to use for user roles.

#### Note: StartTLS

To use a StartTLS connection, specify `"start_tls": true`. When set to `true`, StartTLS is used to secure your connection to the directory service, and any certificates that you have configured through [the DS trust chain setting](./rbac_ldap.html#verify-directory-server-certificates) well be used to verify the identity of the directory service. When specifying StartTLS, make sure that you don't also have SSL set to true.

#### Note: Matching rule in chain

When PE detects an Active Directory that supports the LDAP\_MATCHING\_RULE\_IN\_CHAIN feature, it automatically uses it. Under some specific circumstances, you may need to disable this setting. To disable it, specify `"disable_ldap_matching_rule_in_chain": true` in the PUT request. This is an optional setting.

#### Note: Changing DS settings

When changing directory service settings, you must specify [all of the required directory service settings](./rbac_ldap.html#connect-to-an-external-directory-service) in the PUT request, including the required settings that are remaining the same. You do not need to specify optional settings unless you are changing them.

**Returns:**

* **200 OK** The updated connection settings.

**Example body:**

        {"hostname": "ds.somehost.com",
         "name"
         "port": 10389,
         "login": "frances", ...}

**Example return:**

        {"hostname": "ds.somehost.com",
         "port": 10389,
         "login": "frances", ...}