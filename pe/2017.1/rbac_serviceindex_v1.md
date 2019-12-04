---
layout: default
title: "RBAC endpoints: API v1"
canonical: "/pe/latest/rbac_serviceindex_v1.html"
---

The role-based access control (RBAC) service enables you to manage users, directory groups, and roles.

> **Note:** In addition to the endpoints on this page and in the [v2 RBAC service API](./rbac_serviceindex_v2.html), there are endpoints that you can use to check the health of the RBAC service. These are available through the [status API documentation](./status_api.html#the-status-api).

The service consists of the endpoints below.

## Users
RBAC enables you to manage local users as well as those who are created remotely, on a directory service. With the [`users` endpoints](./rbac_users_v1.html), you can get lists of users, and can create new local users.

## Directory groups
The [`groups` endpoints](./rbac_usergroups_v1.html) enable you to get lists of groups and add a new remote user group.

## Roles
By assigning roles to users, you can manage them in sets that are granted access permissions to various Puppet Enterprise (PE) objects. This makes tracking user access more organized and easier to manage. The [`roles` endpoints](./rbac_roles_v1.html) enable you to get lists of roles and create new roles.

## Permissions
You assign permissions to user roles to manage user access to objects in PE. The [`permissions` endpoints](./rbac_permissionsref_v1.html) enable you to get information about available objects and the permissions that can be constructed for those objects. You can also check an array of permissions.

## Directory service
RBAC enables you to connect with a directory service and work with users and groups already established on your directory service. The [`ds` endpoints](./rbac_dsref_v1.html) enable you to get information about the directory service, test your directory service connection, and replace directory service connection settings.

## Passwords
When users forget passwords or lock themselves out of PE by attempting to log in with incorrect credentials 10 times, you'll have to generate a password reset token for them. The [`password` endpoints](./rbac_passwords_v1.html) enable you to generate password reset tokens for a specific user or with a token that contains a temporary password in the body.

## Token
A user's access to PE services can be controlled using authentication tokens. Users can generate their own authentication tokens using the [`token` endpoint](./rbac_token_v1.html).

## Additional RBAC service information

### Errors
Describes the [errors](./rbac_serviceerrors_v1.html) you might receive when making RBAC service calls.

### Configuration options
Describes RBAC [configuration options](./rbac_config_v1.html), such as how long a password reset token remains valid or how long before a session times out.


