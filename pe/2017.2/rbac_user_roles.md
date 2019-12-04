---
layout: default
title: "Creating and managing users and user roles"
canonical: "/pe/latest/rbac_user_roles.html"
---

## Introduction to users and user roles

Puppet Enterprise's role-based access control (RBAC) enables you to manage users &#8212; what they can create, edit, or view, and what they can't &#8212; in an organized, high-level way that is vastly more efficient than managing user permissions on a per-user basis. **User roles** are sets of permissions you can apply to multiple users. You can't assign permissions to single users in PE, only to user roles. 

> **Note:** Each user must be assigned to one or more roles before they can log in and use PE. 

RBAC has four predefined roles: Administrators, Code Deployers, Operators, and Viewers. These roles have each been given a handful of permissions based on their function. You can also define your own custom roles.

This section describes how to create a new user, create a new user role, assign permissions to a user role, and add a user to a user role. You'll also find out how to import a user group from an external directory and then assign the group to a user role. Finally, we'll discuss deleting users and user groups. 

 > **Note:** Puppet stores local accounts and directory service integration credentials securely. Local account passwords are hashed using SHA-256 multiple times along with a 32-bit salt. Directory service lookup credentials configured for directory lookup purposes are encrypted using AES-128. Puppet does not store the directory credentials used for authenticating to Puppet. These are different from the directory service lookup credentials.

### Create a new user

These steps add a local user. To add a user from an external directory, see our guide to [working with users and user groups from an external directory](./rbac_user_roles.html#working-with-user-groups-and-users-from-an-external-directory-service).

1. In the console, click **Users**.
2. On the **Users** page, in the **Full name** field, type the user's full name.
3. In the **Login** field, type a username for the user.
4. Click **Add local user**.

### Give the user access

When you create new local users, you need to send them a password reset token so that they can log in for the first time.

1. Click the new local user in the **Users** list. The new user's page opens.
2. On the upper-right of the page, click **Generate password reset**. A **Password reset link** message box opens.
3. Copy the link provided in the message and send it to the new user. 

### Create a new user role

Users with the appropriate permissions, such as Administrators, can define custom roles. To avoid potential privilege escalation, only users who are allowed all permissions should be given the permission to edit user roles.

1. In the console, click **User Roles**.
2. In the **Name** field, type a name for the role, and then add a description of the role in the **Description** field. 
3. Click **Add role**.

### Assign permissions to a user role

Before you begin assigning permissions to a user role, make sure you review the information on [RBAC permissions](./rbac_permissions.html). In particular, the [assigning permissions to roles](./rbac_permissions.html#what-you-should-know-about-assigning-permissions) section includes important information about how permissions work in PE.

1. On the **User Roles** page, click a user role that you want to add permissions to.
2. Click the **Permissions** tab.
3. In the **Type** box, select the type of object you want to assign permissions for, such as **Node groups**.
4. In the **Permission** box, select the permission you want to assign, such as **View**.
5. In the **Object** box, select the specific object you want to assign the permission to. For example, if you are setting a permission to view the type, **Node groups**, select the specific node this user role will have permissions to view.
6. Click **Add permission**, and then click the **Commit change** button.

### Add a user to a user role

When you add users to a role, the user gains the permissions that are applied to that role. As has been mentioned, a user can't do anything in PE until they have been assigned to a role.

1. On the **User Roles** page, click a user role that you want to add a user to.
2. Click the **Member users** tab.
3. In the **User name** field, from the drop-down list, select the user you want to add,  click **Add user**, and then click the commit button.

To remove a user from a user role, navigate to the **Member users** tab of the relevant role, click the **Remove** button at the right of the user listing, and confirm your action.   

### Revoke a user’s access

If you want to remove a user's access to PE but not delete their account, you can revoke them. Revocation is also what happens when a user is locked out from too many incorrect password attempts.

To revoke a user:

1. In the left pane of the console, click **Users**.
2. Locate and click the user that you want to revoke.
3. Click **Revoke user access**.

To unrevoke a user, follow the steps above and click **Reinstate user access**.

### Delete a user

You can easily delete a PE user through the console. Note, however, that this action only deletes the user's Puppet Enterprise account, not the user's listing in any external directory service. 

To delete a user: 

1. In the left pane of the console, click **Users**.
2. Locate the user that you want to delete.
3. Click the **Remove** button at the right of the user listing, and then confirm your action. 

> **Note:** Users with superuser privileges cannot be deleted, and the **Remove** button will not appear for these users. 

You can also delete a user through [the RBAC API](./rbac_users_v1.html#delete-usersltsidgt). Either method removes all data about the user except for their activity data, which will continue to be stored in the database and remain viewable through the API.

> **Note:** If a user is deleted from the console and then recreated with the same full name and login, PE issues the recreated user a new unique user ID. In this instance, queries for the login to the API's activity database return information on both the deleted user and the new user. However, in the console, the new user's Activity tab does not display information about the deleted user's account. 

## Working with user groups and users from an external directory service

You import existing external directory groups to PE explicitly, which means you add the group by name. After you’ve imported a group, you can assign it a user role, which gives each group member the permissions associated with that role. 

### Add external directory users to PE

You don’t explicitly add remote users to PE. Instead, remote users must log into PE, which creates a record of the user in PE. If the user belongs to an external directory group that has been imported into PE and then assigned to a role, the user is assigned to that role and gains the privileges of the role. Roles are additive: You can assign users to more than one role, and they gain the privileges of all the roles to which they are assigned.

### Import a user group

1. Click **User Groups**.

   > **Note:** **User Groups** is not available until you have established a connection with an external directory.

2. In the **Login** field, type the name of a group from your directory service, and then click **Add group**. The group is added to the **User Groups** list.

3. Click the name of the group you added.

	A new page opens with tabs for **User roles**, **Users**, and **Activity**. Note that no user roles are listed until you add this group to a role. No users are listed until a user who belongs to this group logs into PE. The **Activity** tab lists any changes made to the group. If you have just imported a group, the date the group was added is listed, as well as some other pertinent information.

### Assign a user group to a user role

You can add user groups to existing roles, or you can [create a new role](./rbac_user_roles.html#create-a-new-user-role), and then add the group to the new role. Assign user groups to user roles as follows:

1. Click **User Roles**, and then click the role you want to add the user group to.
2. Click **Member groups**. In the **Group name** drop-down list, select the user group you want to add to the user role.

   > **Note:** **Member groups** is not available until you have established a connection with an external directory.

3. Click **Add group**, and then click the commit button.

### Delete a user group

You can easily delete a user group in the console. Users who were part of the deleted group lose the permissions associated with roles assigned to the group. 

To delete a user group: 

1. In the PE console, click **User Groups**.
2. Locate the group that you wish to delete. (Remember, **User Groups** is only available if you have established a connection with an external directory.)
3. Click the **Remove** button at the right of the group listing, and then confirm your action. 

> **Note:** This action will only remove the group from Puppet Enterprise, not from the associated external directory service. 

### Remove a remote user’s access

It is important to remember that **simply deleting a remote user's PE account will not automatically prevent that user from accessing PE in the future.** So long as the remote user is still a member of a group in an external directory that PE is configured to access, the user retains the ability to log into PE. In order to fully revoke the remote user's PE access, you must also remove the user from the external directory group(s) accessed by PE. 

If you delete a user from your external directory service but not from PE, the user can no longer log in, but any generated tokens or existing console sessions continue to be valid until they expire. To invalidate the user's tokens or sessions, [revoke](./rbac_user_roles.html#revoke-a-users-access) the user's account in PE, which also automatically revokes all tokens for the user. You must manually delete the user from PE for their account record to disappear.

### Important notes on naming user groups

**An error results when a PE user and user group have the same name** <!--PE-6744-->
If you have both a PE user and an external directory user group with the exact same name, PE throws an error when you try to log on as that user or import the user group. To work around this problem, you can change your settings to use different RDNs for users and groups. This works as long as all of your users are contained under one RDN that is unique from the RDN that contains all of your groups.

**Multiple admins in a directory service block the PE admin** <!--PE-12204-->
If your directory contains multiple users with a login name of "admin," the PE admin account is unable to log in.

If you are locked out of PE as the admin user and there are no other users with administrator access that you can use to reset the access control settings in the console, you can SSH into the box and use cURL to reset the directory service settings to allow admin access again.

For a box named centos7 the cURL call looks like:

```
`curl -X PUT --cert /etc/puppetlabs/puppet/ssl/certs/centos7.pem --key /etc/puppetlabs/puppet/ssl/private_keys/centos7.pem --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem -H "Content-Type: application/json" -d {} https://centos7:4433/rbac-api/v1/ds`
```

## Managing your user information

To view and update your user information, in the left panel of the PE console, click **My Account**.

## Troubleshooting admin and user access to the console

For any number of reasons, you might need to either reset the admin password for PE console access, or the passwords of other users. The process is different in each case.

### Reset the admin password

In RBAC, one of the built-in users is the admin, a superuser with all available read/write privileges. To reset the admin password for console access, you'll need to run the `set_console_admin_password.rb` utility script as follows:

1. Log in to the console node as the `root` user.

   > **Note:** The script must be run from the command line of the server installed with the console component. In a [split install](./install_pe_split.html), it cannot be run from the Puppet master.

2. Run the `set_console_admin_password.rb` script.

   ~~~
   sudo /opt/puppetlabs/puppet/bin/ruby /opt/puppetlabs/server/data/enterprise/modules/pe_install/files/set_console_admin_password.rb <YOUR NEW PASSWORD>
   ~~~

### Generate a user password reset token

When users forget passwords or lock themselves out of the console by attempting to log in with incorrect credentials too many times, you need to generate a password reset token, either from the console or by using API calls. To generate a password reset token from the console, see the steps in the [Give the user access](./rbac_user_roles.html#give-the-user-access) section. To learn more about using API calls to generate the token, see the [RBAC service password APIs](./rbac_passwords_v1.html).

