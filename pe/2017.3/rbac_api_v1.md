---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# RBAC API v1

The role-based access control \(RBAC\) service enables you to manage users, directory groups, and roles.

-   **[Endpoints](rbac_api_v1_endpoints.md#)**  
The role-based access control \(RBAC\) service enables you to manage users, directory groups, and roles.
-   **[Forming RBAC API requests](rbac_api_v1_forming_requests.md#)**  
Web session authentication is required to access the RBAC API. You can authenticate requests by using either user authentication tokens or whitelisted certificates.
-   **[Users endpoints](rbac_api_v1_user.md#)**  
RBAC enables you to manage local users as well as those who are created remotely, on a directory service. With the `users` endpoints, you can get lists of users and create new local users.
-   **[User group endpoints](rbac_api_v1_user_group.md#)**  
Groups are used to assign roles to a group of users, which is vastly more efficient than managing roles for each user individually. The `groups` endpoints enable you to get lists of groups, and to add a new directory group.
-   **[User roles endpoints](rbac_api_v1_user_role.md#)**  
By assigning roles to users, you can manage them in sets that are granted access permissions to various PE objects. This makes tracking user access more organized and easier to manage. The `roles` endpoints enable you to get lists of roles and create new roles.
-   **[Permissions endpoints](rbac_api_v1_permissions.md#)**  
You assign permissions to user roles to manage user access to objects. The `permissions` endpoints enable you to get information about available objects and the permissions that can be constructed for those objects.
-   **[Token endpoints](rbac_api_v1_token.md#)**  
A user's access to PE services can be controlled using authentication tokens. Users can generate their own authentication tokens using the `token` endpoint.
-   **[Directory service endpoints](rbac_api_v1_directory.md#)**  
RBAC enables you to connect with a directory service and work with users and groups already established in your directory. The `ds` \(directory service\) API endpoints enable you to get information about the directory service, test your directory service connection, and replace directory service connection settings.
-   **[Password endpoints](rbac_api_v1_password.md#)**  
When local users forget passwords or lock themselves out of PE by attempting to log in with incorrect credentials too many times, you'll have to generate a password reset token for them. The `password` endpoints enable you to generate password reset tokens for a specific local user or with a token that contains a temporary password in the body.
-   **[RBAC service errors](rbac_api_v1_service_errors.md#)**  
You’re likely to encounter some errors when using the RBAC API. You’ll want to familiarize yourself with the error response descriptions and the general error responses.
-   **[Configuration options](rbac_api_v1_config.md#)**  
There are various configuration options for the RBAC service. Each section can exist in its own file or in separate files.

