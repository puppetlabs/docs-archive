---
layout: default
title: "Role-based access control"
canonical: "/pe/latest/rbac_intro.html"
---

In Puppet Enterprise (PE), role-based access control (RBAC) is used to manage user permissions. Permissions define what actions users can perform on designated objects. For example:

- *Can the user grant password reset tokens to other users who have forgotten their passwords?*

- *Can the user edit a local user's metadata?*

- *Can the user edit class parameters in a node group?*

Permissions are assigned to *user roles* rather than directly to users. To grant a permission to a user, you first need to assign the user to one or more user roles. Users inherit all of the permissions from each user role they are in. PE ships with three default user roles: Administrators, Operators, and Viewers. In addition, you can create custom roles.

By using permissions, you give the appropriate level of access and agency to each user who works with PE. For example, you might want to create a user role that grants users permission to view but not edit a specific subset of node groups. Or you might want to divide up administrative privileges so that one user role is able to reset passwords while another can edit roles and create users. A full list of available permissions is available in [the RBAC permissions overview](./rbac_permissions.html).

## External directories
PE can connect to external LDAP directories. This means that you can create and manage users locally in PE, import users and groups from an existing directory, or do a combination of both. PE supports OpenLDAP and Active Directory. If you have predefined groups in your Active Directory or OpenLDAP directory, you can import these groups into the PE console and assign user roles to them. Users in an imported group inherit the permissions specified in assigned user roles. If new users are added to the group in the external directory, they also inherit the permissions of the role to which that group belongs.

## RBAC and activity services
Access control is handled by the RBAC service, and activity within the RBAC system is recorded by the activity service. You can interact with these two services through the PE console. Alternatively, you can use the [RBAC service API](./rbac_serviceindex_v1.html) and the [activity service API](./rbac_activityapis.html). The RBAC service manages users, user groups, user roles, permissions, authentication tokens, external directory connections, and passwords. The activity service logs events for user roles, users, and user groups.

For more information about using RBAC, see:

* [Connecting Puppet Enterprise with external directory services](./rbac_ldap.html)
* [RBAC permissions](./rbac_permissions.html)
* [Working with user roles](./rbac_user_roles.html)
	* [Creating custom roles](./rbac_user_roles.html#create-a-new-user-role)
	* [Assigning permissions to a user role](./rbac_user_roles.html#assign-permissions-to-a-user-role)
	* [Adding users to roles](./rbac_user_roles.html#add-a-user-to-a-user-role)
	* [Working with users and user groups from an external directory service](./rbac_user_roles.html#working-with-user-groups-and-users-from-an-external-directory-service)
* [RBAC service endpoints for API version 1](./rbac_serviceindex_v1.html) and [version 2](./rbac_serviceindex_v2.html)
* [Activity service endpoints](./rbac_activityapis.html)






