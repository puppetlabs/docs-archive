# User permissions and user roles

The "role" in role-based access control refers to a system of user roles, which are assigned to user groups and their users. Those roles contain permissions, which define what a user can or can't do within PE.

When you add new users to PE, they can't actually do anything until they're associated with a user role, either explicitly through role assignment or implicitly through group membership and role inheritance. When a user is added to a role, they receive all the permissions of that role.

There are four default user roles: Administrators, Code Deployers, Operators, and Viewers. In addition, you can create custom roles.

For example, you might want to create a user role that grants users permission to view but not edit a specific subset of node groups. Or you might want to divide up administrative privileges so that one user role is able to reset passwords while another can edit roles and create users.

Permissions are additive. If a user is associated with multiple roles, that user is able to perform all of the actions described by all of the permissions on all of the applied roles.

## Structure of user permissions

User permissions are structured as a triple of *type*, *permission*, and *object*.

-   **Types** are everything that can be acted on, such as node groups, users, or user roles.
-   **Permissions** are what you can do with each type, such as create, edit, or view.
-   **Objects** are specific instances of the type.

Some permissions added to the Administrators user role might look like this:

|Type|Permission|Object|Description|
|----|----------|------|-----------|
|Node groups|View|PE Master|Gives permission to view the PE Master node group.|
|User roles|Edit|All|Gives permission to edit all user roles.|

When no object is specified, a permission applies to all objects of that type. In those cases, the object is “All”. This is denoted by `"*"` in the API.

In both the console and the API, `"*"` is used to express a permission for which an object doesn’t make sense, such as when creating users.

## User permissions

The table below lists the available object types and their permissions, as well as an explanation of the permission and how to use it.

Note that types and permissions have both a display name, which is the name you see in the console interface, and a system name, which is the name you use to construct permissions in the API. In the following table, the display names are used.

|Type|Permission|Definition|
|----|----------|----------|
|Certificate request|Accept and reject|Accept and reject certificate signing requests. Object must always be `"*"`.|
|Console|View|View the PE console. Object must always be `"*"`.|
|Directory service|View, edit, and test|View, edit, and test directory service settings. Object must always be `"*"`.|
|Job orchestrator|Start, stop and view jobs|Start and stop jobs and tasks, view jobs and job progress, and view an inventory of nodes that are connected to the PCP broker.|
|Node groups|Create, edit, and delete child groups|Create new child groups, delete existing child groups, and modify every attribute of child groups except environment. This permission is inherited by all descendents of the node group.|
|Node groups|Edit child group rules|Edit the rules of descendents of a node group. This does not grant the ability to edit the rules of the group in the `object` field, only children of that group. This permission is inherited by all descendents of the node group.|
|Node groups|Edit classes, parameters, and variables|Edit every attribute of a node group except its environment and rule. This permission is inherited by all descendents of the node group.|
|Node groups|Edit configuration data|Edit parameterized configuration data on a node group. This permission is inherited by all descendents of the node group.|
|Node groups|Edit parameters and variables|Edit the class parameters and variables of a node group's classes. This permission is inherited by all descendents of the node group.|
|Node groups|Set environment|Set the environment of a node group. This permission is inherited by all descendents of the node group.|
|Node groups|View|See all attributes of a node group, most notably the values of class parameters and variables. This permission is inherited by all descendents of the node group.|
|Nodes|Edit node data from PuppetDB|Edit node data imported from PuppetDB. Object must always be `"*"`.|
|Nodes|View node data from PuppetDB|View node data imported from PuppetDB. Object must always be `"*"`.|
|Nodes|View sensitive connection information in inventory service|View sensitive parameters stored in the inventory service for a connection. For example, user credentials. Object must always be `"*"`.|
|Plans|Run plans|Run specific plans on all nodes.|
|Puppet agent|Run Puppet on agent nodes|Trigger a Puppet run from the console or orchestrator. Object must always be `"*"`.|
|Puppet environment|Deploy code|Deploy code to a specific PE environment.|
|Puppet Server|Compile catalogs for remote nodes|Compile a catalog for any node managed by this PE instance. This permission is required to run impact analysis tasks in Continuous Delivery for Puppet Enterprise.|
|Tasks|Run tasks|Run specific tasks on all nodes, a selected node group, or nodes that match a PQL query.**Important:** A task must be permitted to run on all nodes in order to run on nodes that are outside of the PuppetDB \(over SSH or WinRM for example\). As a result, users with such permissions can run tasks on any nodes they have the credentials to access.

|
|User groups|Delete|Delete a user group. This can be granted per group.|
|User groups|Import|Import groups from the directory service for use in RBAC. Object must always be `"*"`.|
|User roles|Create|Create new roles. Object must always be `"*"`.|
|User roles|Edit|Edit and delete a role. Object must always be `"*"`.|
|User roles|Edit members|Change which users and groups a role is assigned to. This can be granted per role.|
|Users|Create|Create new local users. Remote users are "created" by that user authenticating for the first time with RBAC. Object must always be `"*"`.|
|Users|Edit|Edit a local user's data, such as name or email, and delete a local or remote user from PE. This can be granted per user.|
|Users|Reset password|Grant password reset tokens to users who have forgotten their passwords. This process also reinstates a user after the use has been revoked. This can be granted per user.|
|Users|Revoke|Revoke or disable a user. This means the user is no longer able to authenticate and use the console, node classifier, or RBAC. This permission also includes the ability to revoke the user's authentication tokens. This can be granted per user.|

### Display names and corresponding system names

The following table provides both the display and system names for the types and all their corresponding permissions.

|Type \(display name\)|Type \(system name\)|Permission \(display name\)|Permission \(system name\)|
|---------------------|--------------------|---------------------------|--------------------------|
|Certificate requests|cert\_requests|Accept and reject|accept\_reject|
|Console|console\_page|View|view|
|Directory service|directory\_service|View, edit, and test|edit|
|Job orchestrator|orchestrator|Start, stop and view jobs|view|
|Node groups|node\_groups|Create, edit, and delete child groups|modify\_children|
|Node groups|node\_groups|Edit child group rules|edit\_child\_rules|
|Node groups|node\_groups|Edit classes, parameters, and variables|edit\_classification|
|Node groups|node\_groups|Edit configuration data|edit\_config\_data|
|Node groups|node\_groups|Edit parameters and variables|edit\_params\_and\_vars|
|Node groups|node\_groups|Set environment|set\_environment|
|Node groups|node\_groups|View|view|
|Nodes|nodes|Edit node data from PuppetDB|edit\_data|
|Nodes|nodes|View node data from PuppetDB|view\_data|
|Nodes|nodes|View sensitive connection information in inventory service|view\_inventory\_sensitive|
|Plans|plans|Run Plans|run|
|Puppet agent|puppet\_agent|Run Puppet on agent nodes|run|
|Puppet environment|environment|Deploy code|deploy\_code|
|Puppet Server|puppetserver|Compile catalogs for remote nodes|compile\_catalogs|
|Tasks|tasks|Run Tasks|run|
|User groups|user\_groups|Import|import|
|User roles|user\_roles|Create|create|
|User roles|user\_roles|Edit|edit|
|User roles|user\_roles|Edit members|edit\_members|
|Users|users|Create|create|
|Users|users|Edit|edit|
|Users|users|Reset password|reset\_password|
|Users|users|Revoke|disable|

**Related information**  


[Permissions endpoints](rbac_api_v1_permissions.md#)

## Working with node group permissions

Node groups in the node classifier are structured hierarchically; therefore, node group permissions inherit. Users with specific permissions on a node group implicitly receive the permissions on any child groups below that node group in the hierarchy.

Two types of permissions affect a node group: those that affect a group itself, and those that affect the group's child groups. For example, giving a user the "Set environment" permission on a group allows the user to set the environment for that group and all of its children. On the other hand, assigning "Edit child group rules" to a group allows a user to edit the rules for any child group of a specified node group, but not for the node group itself. This allows some users to edit aspects of a group, while other users can be given permissions for all children of that group without being able to affect the group itself.

Due to the hierarchical nature of node groups, if a user is given a permission on the default \(All\) node group, this is functionally equivalent to giving them that permission on `"*"`.

## Best practices for assigning permissions

Working with user permissions can be a little tricky. You don't want to grant users permissions that essentially escalate their role, for example. The following sections describe some strategies and requirements for setting permissions.

### Grant edit permissions to users with create permissions

Creating new objects doesn't automatically grant the creator permission to view those objects. Therefore, users who have permission to create roles, for example, must also be given permission to edit roles, or they won't be able to see new roles that they create. Our recommendation is to assign users permission to edit all objects of the type that they have permission to create. For example:

|Type|Permission|Object|
|----|----------|------|
|User roles|Edit members|All \(or `"*"`\)|
|Users|Edit|All \(or `"*"`\)|

### Avoid granting overly permissive permissions

Operators, a default role in PE, have many of the same permissions as Administrators. However, we've intentionally limited this role's ability to edit user roles. This way, members of this group can do many of the same things as Administrators, but they can't edit \(or enhance\) their own permissions.

Similarly, avoid granting users more permissions than their roles allow. For example, if users have the `roles:edit:*` permission, they are able to add the `node_groups:view:*` permission to the roles they belong to, and subsequently see all node groups.

### Give permission to edit directory service settings to the appropriate users

The directory service password is not redacted when the settings are requested in the API. Give `directory_service:edit:*` permission only to users who are allowed see the password and other settings.

### The ability to reset passwords should be given only with other password permissions

The ability to help reset passwords for users who forgot them is granted by the `users:reset_password:<instance>` permission. This permission has the side effect of reinstating revoked users after the reset token is used. As such, the reset password permission should be given only to users who are also allowed to revoke and reinstate other users.

