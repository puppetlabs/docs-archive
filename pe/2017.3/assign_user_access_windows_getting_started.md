# Getting started: Assigning user access

After adding classes to your Puppet agent, you can assign user access. The console enables you to import users and groups, create user roles, and assign users to roles. In this exercise, you create a new user role, and give the role view permissions on the node group you previously created. Then you create a new local user, and assign a user role to that user.

You can connect Puppet Enterprise \(PE\) with an external directory, such as Active Directory or OpenLDAP, and import users and groups, rather than creating and maintaining users and groups in multiple locations. You can create user roles, and assign imported users to those roles. Roles are granted permissions, such as permission to act on node groups. When you assign roles to users or user groups, you are granting users permissions in a more organized way.

This exercise doesn't cover connecting with an OpenLDAP or Active Directory.

**Note:** Users and user groups are not currently deletable. And roles are deletable by API, not in the console. Therefore, we recommend that you try out these steps on a virtual machine.

## Create a new user role

Add a user role so you can manage permissions for groups of users at one time.

### Before you begin

Ensure you have installed a monolithic PE deployment, installed at least one Windows agent node, installed the `puppetlabs-wsus_client` module, and classified a node.

You must have admin permissions to complete these steps, which include assigning a user to a role.

### About this task

### Procedure

1.  In the console, click **User Roles**.

2.  For **Name**, type Windows users, and then for **Description**, type a description for the role, such as Windows users.

3.  Click **Add role**.


### Results

## Create a new user and add the user to your new role

These steps demonstrate how to create a new local user.

### About this task

### Procedure

1.  In the console, click **Users**.

2.  In the **Full name** field, type in a user name.

3.  In the **Login** field, type a username for the user.

4.  Click **Add local user**.

    **Note:** When you create new local users, you need to send them a login token. Do this by clicking the new user's name in the **User list** and then on the upper-right of the user's page, click **Generate password rese**t. A message opens with a link that you must copy and send to the new user.

5.  Click User Roles and then click Windows users.

6.  On the **Member** users tab, on the **User name** list, select the new user you created, and then click **Add user** and click the commit button.


### Results

## Enable a user to log in

When you create new local users, you need to send them a password reset token so that they can log in for the first time.

### About this task

### Procedure

1.  On the **Users** page, click the new local user. The new user's page opens.

2.  On the upper-right of the page, click **Generate password reset**. A **Password reset link** message box opens.

3.  Copy the link provided in the message and send it to the new user. Then you can close the message.


## Give your new role access to the node group you created

You must give the role access to the group, so that the `Windows users` role can view the `windows_example` node group.

### About this task

### Procedure

1.  From the **Windows users role**page, click the **Permissions** tab.

2.  In the **Type** box, select **Node groups**.

3.  In the **Permission** box, select **View**.

4.  In the **Object** box, select `windows_exampl`e.

5.  Click **Add permission**, and then click the commit button.


