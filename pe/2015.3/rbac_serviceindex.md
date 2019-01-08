---
layout: default
title: "RBAC Endpoints"
canonical: "/pe/latest/rbac_serviceindex.html"
---

The role-based access control (RBAC) service enables you to manage users, directory groups, and roles.

> **Note:** In addition to the endpoints on this page, there are some endpoints that you can use to check the health of the RBAC service. These are available through the [Status API documentation](./status_api.html).

The service consists of the endpoints below.

## [Users](./rbac_users.html)
RBAC enables you to manage local users as well as those who are created remotely, on a directory service. With the users endpoints, you can get lists of users, and you can create new local users.

## [Directory Groups](./rbac_usergroups.html)
The `groups` endpoints enable you to get lists of groups and add a new remote user group.

## [Roles](./rbac_roles.html)
By assigning roles to users, you can manage them in sets that are granted access permissions to various Puppet Enterprise (PE) objects. This makes tracking user access more organized and easier to manage. The `roles` endpoints enable you to get lists of roles and create new roles.

## [Permissions](./rbac_permissionsref.html)
You assign permissions to user roles to manage user access to objects in PE. The `permissions` endpoints enable you to get information about available objects and the permissions that can be constructed for those objects. You can also check an array of permissions.

## [Directory Service](./rbac_dsref.html)
RBAC enables you to connect with a directory service and work with users and groups already established on your directory service. The `ds` endpoints enable you to get information about the directory service, test your directory service connection, and replace directory service connection settings.

## [Passwords](./rbac_passwords.html)
When users forget passwords or lock themselves out of PE by attempting to log in with incorrect credentials 10 times, you'll have to generate a password reset token for them. The `password` endpoints enable you to generate password reset tokens for a specific user or with a token that contains a temporary password in the body.

## [Token](./rbac_token.html)
A user's access to PE services can be controlled using authentication tokens. Users can generate their own authentication tokens using the `token` endpoint.

## Additional RBAC Service Information

## [Errors](./rbac_serviceerrors.html)
Describes the errors you might receive when making RBAC service calls.

## [Configuration Options](./rbac_config.html)
Describes RBAC configuration options for things, such as how long a password reset token remains valid or how long before a session will time out.


