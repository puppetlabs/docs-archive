---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Adding users and creating groups

Continuous Delivery for Puppet Enterprise \(PE\) is built to facilitate collaboration between the members of your team. You can share resources by adding users to your team's account, set up groups to facilitate collaboration, and manage the permissions granted to each member of your team.

**Tip:** Set up an account for your team or organization, and add individual users to that account. This allows you to create groups based on user roles and manage each group's level of permissions in Continuous Delivery for PE.

## Add a user

Adding users allows you to collaborate on projects and share account resources such as jobs and pipelines. You can add users in the Account Settings area of the Continuous Delivery for PE web UI.

### Before you begin

Make sure each user you wish to add has signed up for a Continuous Delivery for PE account. You'll need their selected username or the email address they used when signing up.  

### About this task

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Settings**.

2.  In the **Users** tab, click **+ Add User\(s\)**. The Add User pane opens.

3.  Enter the username or email address of the user you wish to add to your account. Partial matches are allowed.

4.  When you locate the user you wish to add to your account, click **Add User** ![](add_user.png) and confirm your action.

5.  Repeat these steps to add additional users.


### Result:

When you're finished adding users, close the Add User pane to return to your Users screen, where you'll see the full list of users with access to your Continuous Delivery for PE instance.

## Create a new group

You can set up groups to organize users by permission level, job focus, geography, or other criteria.

### About this task

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Settings**.

2.  In the **Groups** tab, click **+ Create Group**.

3.  Enter a name and description for your new group.

    **Tip:** Use a name for your group that describes the group's function or permission level, such as "Read-only users" or "Module developers."

    CAUTION:

    Once submitted, group names and descriptions cannot be edited. If you wish to change the name or description of your group, you must delete the group and recreate it.

4.  Click **Create Group**.


### Result:

## Add or remove group members

Grant users membership in groups by adding them in the Continuous Delivery for PE web UI. This is also where you can remove users from groups. 

### Before you begin

Add the users you wish to include in your group, and create a new group.

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Settings**.

2.  In the **Groups** tab, locate the group whose membership you wish to update, and click **View Group Details** ![](view_details.png).

3.  In the Group Details and Permissions pane, locate the **Group Members** column.

    -   To add a new member to the group, click **+ Add Member**, and search for the user you wish to add by username or email address. When you locate the user you wish to add, click **Add User**.

    -   To remove an existing member from the group, click **Remove Group Member** ![](remove_user_icon.png) and confirm your action.


## Assign permissions to a group

Permissions are the tasks members of a group can perform in Continuous Delivery for PE, such as adding new control repos, editing jobs, and deploying code changes. Assign permissions to a group based on that group's function and needs.

### Before you begin

Create a group and add group members.

### About this task

Group permissions are additive. If a user is a member of multiple groups, that user is able to perform all of the actions described by all of the permissions in all their assigned groups.

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Settings**.

2.  In the **Groups** tab, locate the group whose permissions you wish to set, and click **View Group Details** ![](view_details.png).

3.  In each area, such as Modules or Integrations, click **+ Set Permissions**. Select the permissions you wish to assign to the group and click the blue **Set Permissions** button.

    For an explanation of the scope of each permission, see the [Permissions reference](permissions_reference.md).

    **Note:** The permission to change a user's email address and password is granted only to super users. For more on super users, see [Using the root console](the_root_console.md#).

4.  When you've finished setting permissions, click **Close** and review the summary of the permissions now granted to your group.


### Result:

## Remove a user from a workspace

If you wish to remove a user from Continuous Delivery for PE workspace, you can do so in the Account Settings area of the Continuous Delivery for PE web UI.

### About this task

**Note:** If you remove a user from a Continuous Delivery for PE workspace, that user is automatically removed from all groups in that workspace.  

### Procedure

1.  In the Continuous Delivery for PE web UI, click **Settings** then click **Users**.

2.  In the list of users, locate the user you wish to remove from your workspace.

3.  Click **Remove User** ![](remove_user_icon.png) and confirm your action.

4.  Repeat these steps to remove additional users from the workspace.


