# Managing access

Role-based access control, more succinctly called RBAC, is used to grant individual users the permission to perform specific actions. Permissions are grouped into user roles, and each user is assigned at least one user role.

By using permissions, you give the appropriate level of access and agency to each user. For example, you can grant users:

-   The permission to grant password reset tokens to other users who have forgotten their passwords
-   The permission to edit a local user’s metadata
-   The permission to deploy Puppet code to specific environments
-   The permission to edit class parameters in a node group

You can do access control tasks in the console or using the RBAC API.

-   **[User permissions and user roles](rbac_permissions_intro.md#)**  
The "role" in role-based access control refers to a system of user roles, which are assigned to user groups and their users. Those roles contain permissions, which define what a user can or can't do within PE.
-   **[Creating and managing local users and user roles](rbac_user_roles_intro.md#)**  
Puppet Enterprise's role-based access control \(RBAC\) enables you to manage users—what they can create, edit, or view, and what they can't—in an organized, high-level way that is vastly more efficient than managing user permissions on a per-user basis. User roles are sets of permissions you can apply to multiple users. You can't assign permissions to single users in PE, only to user roles.
-   **[Connecting external directory services to PE](rbac_ldap_intro.md#)**  
 Puppet Enterprise connects to external Lightweight Directory Access Protocol \(LDAP\) directory services through its role-based access control \(RBAC\) service. This allows you to use existing users and user groups that have been set up in your external directory service.
-   **[Working with user groups from an external directory service](rbac_user_roles_user_groups_ex_dir.md#)**  
You don’t explicitly add remote users to PE. Instead, once the external directory service has been successfully connected, remote users must log into PE, which creates their user record.
-   **[Token-based authentication](rbac_token_auth_intro.md#)**  
Puppet Enterprise users generate tokens to authenticate their access to certain PE command-line tools and API endpoints. Authentication tokens are tied to the permissions granted to the user through RBAC, and provide the user with the appropriate access to HTTP requests.
-   **[RBAC API v1](rbac_api_v1.md)**  
The role-based access control \(RBAC\) service enables you to manage users, directory groups, and roles.
-   **[RBAC API v2](rbac_api_v2_endpoints.md)**  
The role-based access control \(RBAC\) service enables you to manage users, directory groups, and roles.
-   **[Activity service API](activity_api.md)**  
The activity service logs changes to role-based access control \(RBAC\) entities, such as users, directory groups, and user roles.

