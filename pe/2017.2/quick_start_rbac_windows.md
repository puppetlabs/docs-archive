---
layout: default
title: "Assign user access quick start guide"
canonical: "/pe/latest/quick_start_rbac_windows.html"
---

The Puppet Enterprise (PE) console enables you to import users and groups, create user roles, and assign users to roles.

You can connect PE with an external directory, such as Active Directory or OpenLDAP, and import users and groups, rather than creating and maintaining users and groups in multiple locations. You can create user roles, and assign imported users to those roles. Roles are granted permissions, such as permission to act on node groups. When you assign roles to users or user groups, you are granting users permissions in a more organized way.

In this exercise, you create a new user role and give the role view permissions on your node group. Then you create a new local user, and assign a user role to that user. This exercise doesn't cover connecting with an OpenLDAP or Active Directory. For more information about those subjects, see [Working with Role-Based Access Control](./rbac_intro.html).

>**Note**: Users and user groups are not currently deletable. And roles are deletable by API, not in the console. Therefore, we recommend that you try out these steps on a virtual machine.

<!--Task-->
## Create a new user role

Add a user role so you can manage permissions for groups of users at one time.

> **Before you begin**: Install a monolithic PE deployment, install at least one Windows agent node, install the `puppetlabs-wsus_client` module, and classify a node.
>
> Finally, you must have admin permissions to complete these steps, which include assigning a user to a role.

1. In the console, click **User Roles**.
2. For **Name**, type __Windows users__, and then for **Description**, type a description for the role, such as __Windows users__.
3. Click **Add role**.

Related topics: [Install Puppet Enterprise quick start guide](./quick_start_install_mono.html), [Install *nix agents quick start guide](./quick_start_install_agents_windows.html), [Module installation quick start guide](./quick_start_module_install_windows.html), [Adding classes quick start guide](./quick_start_adding_class_windows.html)

<!--Task-->
## Create a new user and add the user to your new role

These steps demonstrate how to create a new local user. See [Adding LDAP Users to PE](./rbac_user_roles.html#add-external-directory-users-to-pe) for information about adding existing users from your directory service.

1. In the console, click **Users**.
2. In the **Full name** field, type in a user name.
3. In the **Login** field, type a username for the user.
4. Click **Add local user**.

	>**Note**: When you create new local users, you need to send them a login token. Do this by clicking the new user's name in the **User** list and then on the upper-right of the user's page, click **Generate password reset**. A message opens with a link that you must copy and send to the new user.

5. Click **User Roles** and then click **Windows users**.
6. On the **Member users** tab, on the **User name** list, select the new user you created, and then click **Add user** and click the commit button.

<!--Task-->
## Enable a user to log in

When you create new local users, you need to send them a password reset token so that they can log in for the first time.

1. On the **Users** page, click the new local user.
The new user's page opens.
2. On the upper-right of the page, click **Generate password reset**. A **Password reset link** message box opens.
3. Copy the link provided in the message and send it to the new user. Then you can close the message.

<!--Task-->
## Give your new role access to the node group you created

You must give the role access to the group, so that the `Windows users` role can view the `windows_example` node group.

1. From the **Windows users** role page, click the **Permissions** tab.
2. In the **Type** box, select **Node groups**.
3. In the **Permission** box, select **View**.
4. In the **Object** box, select **windows_example**.
5. Click **Add permission**, and then click the commit button.


----------

Next: [Writing Modules (Windows)](./quick_start_module_install_windows.html)