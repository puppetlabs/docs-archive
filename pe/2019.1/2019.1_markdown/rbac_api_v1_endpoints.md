# Endpoints

The role-based access control \(RBAC\) service enables you to manage users, directory groups, and roles.

For general information about forming HTTP requests to the API, see the forming requests page. For information on errors encountered while using the RBAC v1 API, see the RBAC service errors page.

**Tip:** In addition to the endpoints on this page and in the v2 RBAC service API, there are endpoints that you can use to check the health of the RBAC service. These are available through the status API documentation.

|Endpoint|Use|
|--------|---|
|`users`|Manage local users as well as those from a directory service, get lists of users, and create new local users.|
|`groups`|Get lists of groups and add a new remote user group.|
|`roles`|Get lists of user roles and create new roles.|
|`permissions`|Get information about available objects and the permissions that can be constructed for those objects.|
|`ds` \(Directory service\)|Get information about the directory service, test your directory service connection, and replace directory service connection settings.|
|`password`|Generate password reset tokens and update user passwords.|
|`token`|Generate the authentication tokens used to access PE.|
|`rbac-service`|Check the status of the RBAC service.|

