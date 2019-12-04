# Working with user groups from an external directory service

You don’t explicitly add remote users to PE. Instead, once the external directory service has been successfully connected, remote users must log into PE, which creates their user record.

If the user belongs to an external directory group that has been imported into PE and then assigned to a role, the user is assigned to that role and gains the privileges of the role. Roles are additive: You can assign users to more than one role, and they gain the privileges of all the roles to which they are assigned.

## Import a user group from an external directory service

You import existing external directory groups to PE explicitly, which means you add the group by name.

### Procedure

1.  In the console, click Access control \> User groups.

    User groups is only available if you have established a connection with an external directory.

2.  In the **Login** field, enter the name of a group from your external directory.

3.  Click **Add group**.

    **Remember:** No user roles are listed until you add this group to a role. No users are listed until a user who belongs to this group logs into PE.


### Troubleshooting: A PE user and user group have the same name

If you have both a PE user and an external directory user group with the exact same name, PE throws an error when you try to log on as that user or import the user group.

To work around this problem, you can change your settings to use different RDNs for users and groups. This works as long as all of your users are contained under one RDN that is unique from the RDN that contains all of your groups.

## Assign a user group to a user role

After you’ve imported a group, you can assign it a user role, which gives each group member the permissions associated with that role. You can add user groups to existing roles, or you can create a new role, and then add the group to the new role.

### About this task

### Procedure

1.  In the console, click Access control \> User roles.

2.  Click the role you want to add the user group to.

3.  Click **Member groups**. In the **Group name** field, select the user group you want to add to the user role.

4.  Click **Add group**, and commit changes.


### Results

## Delete a user group

You can delete a user group in the console. Users who were part of the deleted group lose the permissions associated with roles assigned to the group.

### About this task

**Remember:** This action will only remove the group from Puppet Enterprise, not from the associated external directory service.

### Procedure

1.  In the console, click Access control \> User groups.

    User groups is only available if you have established a connection with an external directory.

2.  Locate the group that you wish to delete.

3.  Click **Remove**.


## Removing a remote user’s access to PE

In order to fully revoke the remote user's access to Puppet Enterprise, you must also remove the user from the external directory group\(s\) accessed by PE.

It is important to remember that simply deleting a remote user's PE account will not automatically prevent that user from accessing PE in the future. So long as the remote user is still a member of a group in an external directory that PE is configured to access, the user retains the ability to log into PE.

If you delete a user from your external directory service but not from PE, the user can no longer log in, but any generated tokens or existing console sessions continue to be valid until they expire. To invalidate the user's tokens or sessions, revoke the user's PE account, which also automatically revokes all tokens for the user. You must manually delete the user for their account record to disappear.

