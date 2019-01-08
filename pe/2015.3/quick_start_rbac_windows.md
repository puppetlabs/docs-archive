---
layout: default
title: "Role-Based Access Control Quick Start Guide"
canonical: "/pe/latest/quick_start_rbac_windows.html"
---

## Overview

The Puppet Enterprise (PE) console enables you connect with an external directory, such as Active Directory or OpenLDAP, and import users and groups, rather than creating and maintaining users and groups in multiple locations. You can also create user roles, and assign users to those roles. Roles are granted permissions, such as permission to act on node groups. When you assign roles to users or user groups, you are granting users permissions in a more organized way.

In this exercise, you'll create a new user role and give the role view permissions on your node group. And you’ll create a new local user, and assign a user role to that user.  This exercise doesn't cover connecting with an OpenLDAP or Active Directory. For more information about that, see [Working with Role-Based Access Control](./rbac_intro.html).

>**Note**: Users and user groups are not currently deletable. And roles are deletable by API, not in the console. Therefore, we recommend that you try out these steps on a virtual machine.

[assign_rule]: ./images/quick/assign_rule.png

> **Prerequisites**: This guide assumes you've already [installed a monolithic PE deployment](./quick_start_install_mono.html), and have installed at least one [Windows node](./quick_start_install_agents_windows.html).
>
> It also assumes you’ve installed a [Windows](./quick_start_module_install_windows.html) module. Finally, you must have admin permissions to complete these steps, which include assigning a user to a role.


## Create a new user role

Add a user role so you can manage permissions for groups of users at once.

1. In the console, click **Access control**. The **User Roles** page should be open.
2. For **Name**, type __Windows users__, and then for **Description**, type a description for the role, such as __Windows users__.
3. Click **Add role**.

## Create a new user and add the user to your new role

These steps demonstrate how to create a new local user. See [Adding LDAP Users to PE](./rbac_user_roles.html#adding-ldap-users-to-pe) for information about adding existing users from your directory service.

1. On the **Access control** page, click **Users**.
2. In the **Full name** field, type in a user name.
3. In the **Login** field, type a username for the user.
4. Click **Add local user**.

	**Note**: When you create new local users, you need to send them a login token. Do this by clicking the new user's name in the **User** list and then on the upper-right of the user's page, click **Generate password reset**. A message opens with a link that you must copy and send to the new user.

5. Click **User Roles** and then click **Windows users**.
6. On the **Member users** tab, on the **User name** list, select the new user you created, and then click **Add user** and click the commit button.

## Enable a User to Log in
When you create new local users, you need to send them a password reset token so that they can log in for the first time.

1. On the **Users** page, click the new local user.
The new user's page opens.
2. On the upper-right of the page, click **Generate password reset**. A **Password reset link** message box opens.
3. Copy the link provided in the message and send it to the new user. Then you can close the message.

## Give Your New Role Access to the Node Group You Created

1. From the **Windows users** role page, click the **Permissions** tab.
2. In the **Type** box, select **Node groups**.
3. In the **Permission** box, select **View**.
4. In the **Object** box, select **windows_example**.
5. Click **Add permission**, and then click the commit button.
Now, you have given members of the `Windows users` role permission to view the `windows_example` node group.


----------

Next: [Writing Modules (Windows)](./quick_start_module_install_windows.html)