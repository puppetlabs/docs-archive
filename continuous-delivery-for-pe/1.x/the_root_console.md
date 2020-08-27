---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Using the root console

Super users and the root user can access the root console in Continuous Delivery for Puppet Enterprise \(PE\). Use the root console to manage users' account credentials, change super user permissions, configure single sign-on, and update your installation's settings.

If you are the root user, access the root console by signing into Continuous Delivery for PE with the root account credentials established during installation.

If you are a super user, access the root console by adding `/root` to the end of the Continuous Delivery for PE web UI endpoint's base URL. To exit the root console, click your username in the user menu ![](user_icon.png).

## Designate super users

Any Continuous Delivery for PE user can be designated as a super user. Super users have access to the root console, where they can reset other users' credentials and update settings.

### About this task

You must have root or super user permissions to access the root console and perform this action.

### Procedure

1.  Log into the root console by adding `/root` to the end of the base URL of the Continuous Delivery for PE web UI or signing in as the root user.

2.  Click **Accounts**.

3.  Locate the user's name in the list and select the **Super User** toggle, then confirm your action.


### Result:

The selected user is now a super user.

When you're finished making changes in the root console, click your username in the user menu ![](user_icon.png) to return to the Continuous Delivery for PE web UI.

## Change a user's password

Users can reset their own passwords at any time by clicking **Forgot your password?** on the Continuous Delivery for PE login screen. If you need to change a user's password on their behalf, or revoke a user's access to Continuous Delivery for PE, use the root console.  

### About this task

You must have root or super user permissions to access the root console and perform this action.

CAUTION:

Resetting a user's password does not automatically log the user out of Continuous Delivery for PE. If you reset a user's password in order to revoke their access to Continuous Delivery for PE, remember that the user can still access their account until their session expires or they log out.

### Procedure

1.  Log into the root console by adding `/root` to the end of the base URL of the Continuous Delivery for PE web UI or signing in as the root user.

2.  Click **Accounts**.

3.  Locate the user whose password you wish to update by searching by username or email address. and click **User Settings** ![](user_settings.png).

4.  Click **Change Password**. Enter and confirm the new password, then click **Change Password**.


### Result:

When you're finished making changes in the root console, click your username in the user menu ![](user_icon.png) to return to the Continuous Delivery for PE web UI.

## Reset a user's email address

Users can change their own email address at any time by clicking **Change email** on the Profile page. If you need to change a user's email address on their behalf, use the root console.  

### About this task

You must have root or superuser permissions to access the root console and perform this action.

### Procedure

1.  Log into the root console by adding `/root` to the end of the base URL of the Continuous Delivery for PE web UI or signing in as the root user.

2.  Click **Accounts**.

3.  Locate the user whose password you wish to update by searching by username or email address. and click **User Settings** ![](user_settings.png).

4.  Enter the new email address for the user and click **Change Email**.


### Result:

When you're finished making changes in the root console, click your username in the user menu ![](user_icon.png) to return to the Continuous Delivery for PE web UI.

