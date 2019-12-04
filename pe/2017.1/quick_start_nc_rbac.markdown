---
layout: default
title: "Assigning user permissions quick start guide"
canonical: "/pe/latest/quick_start_nc_rbac.html"
---
<!--Overview-->
In this quick start guide, you'll create a new user role and give the role the permission to view the node group you created in the [Adding classes quick start guide](./quick_start_adding_class_nix.html). You'll also create a new local user, and assign your new user role to that user.

PE enables you to create and manage **users** and **user groups** through the console. You can also create **user roles**, and assign users to those roles. Permissions, such as the ability to view node groups, deploy code, or generate password reset tokens, are assigned to user roles rather than directly to users. When you assign roles to users or user groups, you are granting permissions in a more organized way.

<!--Multitask-->
## Complete a basic RBAC workflow  

Puppet Enterprise’s role-based access control (RBAC) is used to manage user permissions. Permissions define what actions users can perform on designated objects. There are multiple steps involved in an RBAC workflow, which can be adapted to fit your needs. 

Before you begin, make sure you have installed: 
* A [monolithic PE deployment](./quick_start_install_mono.html)
* At least one [*nix agent node](./quick_start_install_agents_nix.html)
* The [puppetlabs-apache module](./quick_start_module_install_nix.html) 

> **Note:** Roles are deletable by API, not in the console. Therefore, it's best to try out these steps on a virtual machine.

### Step 1: Create a user role

User roles are sets of permissions you can apply to multiple users. You can’t assign permissions to single users in PE, only to user roles.

1. In the console, click **Access control** and then click **User Roles**.
2. For **Name**, type __Web developers__, and then for **Description**, type a description for the Web developers role, such as __Members of the web development team__.
3. Click **Add role**.

### Step 2: Give the user role access to a node group

One of the permissions you can grant to user roles is the ability to access (view, create, and/or edit) node groups. 

1. From the **User roles** page, select **Web developers**, and then click the **Permissions** tab.
2. In the **Type** box, select **Node groups**.
3. In the **Permission** box, select **View**.
4. In the **Object** box, select **apache_example**.
5. Click **Add permission**, and then click the commit button.

You have given members of the `Web developers` role permission to view the `apache_example` node group.

### Step 3: Create a new user

These steps add a local user. You can also [import users and groups from an external directory](./rbac_ldap.html), so you don't have to recreate users one at a time.

1. On the **Access Control** page, click **Users**.
2. In the **Full name** field, type in a user name.
3. In the **Login** field, type the user's login information.
4. Click **Add local user**.

### Step 4: Enable the new user to log in

When you create new local users, you need to send them a password reset token so that they can log in for the first time.

1. Click the new local user in the **Users** list.
The new user's page opens.
2. On the upper-right of the page, click **Generate password reset**. A **Password reset link** message box opens.
3. *Copy the link* provided in the message and send it to the new user. Then you can close the message.

### Step 5: Assign the user role to the new user

Each user must be assigned to one or more roles before they can log in and use PE. When you add users to a role, the user gains the permissions that are applied to that role. 

1. Click **User Roles** and then click **Web developers**.
2. On the **Member users** tab, on the **User name** list, select the new user you created, and then click **Add user** and click the commit button.

You're now managing a user with RBAC. By using permissions and user roles, you give the appropriate level of access and agency to each user or user group who works with PE. 

> Next, you'll learn more about the basics of writing modules of your own, so you can begin customizing your deployment and getting work done. [Click here when you're ready to write your first module.](./quick_writing_nix.html)



