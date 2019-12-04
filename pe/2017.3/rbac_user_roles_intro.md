# Creating and managing local users and user roles

Puppet Enterprise's role-based access control \(RBAC\) enables you to manage users—what they can create, edit, or view, and what they can't—in an organized, high-level way that is vastly more efficient than managing user permissions on a per-user basis. User roles are sets of permissions you can apply to multiple users. You can't assign permissions to single users in PE, only to user roles.

**Remember:** Each user must be assigned to one or more roles before they can log in and use PE.

**Note:** Puppet stores local accounts and directory service integration credentials securely. Local account passwords are hashed using SHA-256 multiple times along with a 32-bit salt. Directory service lookup credentials configured for directory lookup purposes are encrypted using AES-128. Puppet does not store the directory credentials used for authenticating to Puppet. These are different from the directory service lookup credentials.

## Create a new user

These steps add a local user.

### About this task

To add users from an external directory, see [Working with user groups from an external directory](rbac_user_roles_user_groups_ex_dir.md#).

### Procedure

1.  In the console, click Access control \> Users.

2.  In the **Full name** field, enter the user's full name.

3.  In the **Login** field, enter a username for the user.

4.  Click **Add local user**.


## Give a new user access to the console

When you create new local users, you need to send them a password reset token so that they can log in for the first time.

### Procedure

1.  On the Users page, click the user's full name.

2.  Click **Generate password reset**.

3.  Copy the link provided in the message and send it to the new user.


## Create a new user role

RBAC has four predefined roles: Administrators, Code Deployers, Operators, and Viewers. You can also define your own custom user roles.

### About this task

Users with the appropriate permissions, such as Administrators, can define custom roles. To avoid potential privilege escalation, only users who are allowed all permissions should be given the permission to edit user roles.

### Procedure

1.  In the console, click Access control \> User roles.

2.  In the **Name** field, enter a name for the new user role.

3.  \(Optional\) In the **Description** field, enter a description of the new user role.

4.  Click **Add role**.


## Assign permissions to a user role

You can mix and match permissions to create custom user roles that provide users with precise levels of access to PE actions.

### Before you begin

Review [User permissions and user roles](rbac_permissions_intro.md#), which includes important information about how permissions work in PE.

### Procedure

1.  On the User roles page, click a user role.

2.  Click **Permissions**.

3.  In the **Type** field, select the type of object you want to assign permissions for, such as **Node groups**.

4.  In the **Permission** field, select the permission you want to assign, such as **View**.

5.  In the **Object** field, select the specific object you want to assign the permission to. For example, if you are setting a permission to view node groups, select a specific node group this user role will have permissions to view.

6.  Click **Add permission**, and commit changes.


**Related topics**  


[Best practices for assigning permissions](rbac_permissions_intro.md#)

## Add a user to a user role

When you add users to a role, the user gains the permissions that are applied to that role. A user can't do anything in PE until they have been assigned to a role.

### Procedure

1.  On the User roles page, click a user role.

2.  Click **Member users**.

3.  In the **User name** field, select the user you want to add to the user role.

4.  Click **Add user**, and commit changes.


## Remove a user from a user role

You can change a user's permissions by removing them from a user role. The user will lose the permissions associated with the role, and won't be able to do anything in PE until they are assigned to a new role.

### Procedure

1.  On the User roles page, click a user role.

2.  Click **Member users**.

3.  Locate the user you want to remove from the user role. Click **Remove**, and commit changes.


## Revoke a user’s access

If you want to remove a user's access to PE but not delete their account, you can revoke them. Revocation is also what happens when a user is locked out from too many incorrect password attempts.

### Procedure

1.  In the console, click Access control \> Users.

2.  In the **Full name** column, select the user you want to revoke.

3.  Click **Revoke user access**.


### Results

**Tip:** To unrevoke a user, follow the steps above and click **Reinstate user access**.

## Delete a user

You can delete a user through the console. Note, however, that this action only deletes the user's Puppet Enterprise account, not the user's listing in any external directory service.

### About this task

Deletion removes all data about the user except for their activity data, which will continue to be stored in the database and remain viewable through the API.

### Procedure

1.  In the console, click Access control \> Users.

2.  In the **Full name** column, locate the user you want to delete.

3.  Click **Remove**.

    **Note:** Users with superuser privileges cannot be deleted, and the **Remove** button will not appear for these users.


### Results

CAUTION:

If a user is deleted from the console and then recreated with the same full name and login, PE issues the recreated user a new unique user ID. In this instance, queries for the login to the API's activity database return information on both the deleted user and the new user. However, in the console, the new user's **Activity** tab does not display information about the deleted user's account.

