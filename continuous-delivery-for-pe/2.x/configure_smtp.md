---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Configure SMTP

Configure SMTP for your Continuous Delivery for Puppet Enterprise \(PE\) installation so that users can receive email notifications from the software.

## About this task

The root user or a super user must complete this process.

## Procedure

1.  Log into the root console by selecting **Root Console** from the workspaces menu at the top of the Continuous Delivery for PE navigation bar or signing in as the root user.

2.  Click **Settings** \> **SMTP**.

3.  If your SMTP server requires authentication, enter the **SMTP Username** and **SMTP Password** in the relevant fields.

4.  Enter the name of your **SMTP Host** and the **SMTP Port** number in the relevant fields.

    **Note:** Contact your email server administrator if you need help determining the correct SMTP port.

5.  If your server requires TLS authentication, click the toggle to **Enable TLS**.

6.  Click **Save Settings**.

7.  To test your new SMTP configuration, generate a password reset email.

    1.  Log out of Continuous Delivery for PE. On the sign-in screen, click **Forgot your password?**

    2.  Enter the email address associated with your Continuous Delivery for PE account and click **Send Reset Instructions**.

        **Important:** You can safely ignore the reset password instructions and keep your current password.


## Result:

Once SMTP is configured, Continuous Delivery for PE sends email in these situations:

-   Password reset instructions when requested by a user
-   Password reset confirmation once a password is updated
-   Deployment approval request notifications when a [deployment to a protected environment](approval.md) is proposed

