---
layout: default
title: "Accessing the console"
canonical: "/pe/latest/console_accessing.html"
---

The console is Puppet Enterprise's web UI. Use it to:

* Manage node requests to join the Puppet deployment.
* Assign Puppet classes to nodes and groups.
* View reports and activity graphs.
* Browse and compare resources on your nodes.
* View inventory data.
* Manage console users and their access privileges.


Reaching the console
-----

The console is served as a website over SSL, on whichever port you chose when installing the console component.

Let's say your console server is `console.domain.com`. If you chose to use the default port of 443, you can omit the port from the URL and reach the console by navigating to:

<big><strong><code>https://console.domain.com</code></strong></big>

If you chose to use port 3000, you reach the console at:

<big><strong><code>https://console.domain.com:3000</code></strong></big>

Note the **`https`** protocol handler --- you cannot reach the console over plain `http`.

Accepting the console's certificate
-----

The console uses an SSL certificate created by your own local Puppet certificate authority. Since this authority is specific to your site, web browsers won't know it or trust it, and you'll have to add a security exception in order to access the console.

**This is safe to do.** Your web browser will warn you that the console's identity hasn't been verified by one of the external authorities it knows of, but that doesn't mean it's untrustworthy. Since you or another administrator at your site is in full control of which certificates the Puppet certificate authority signs, the authority verifying the site is _you._

### Accepting the certificate in Google Chrome or Chromium

Step 1: When you receive a warning that the connection isn't private, click **Advanced**.

![Screenshot: Chrome showing an untrusted cert warning, with the 'Proceed anyway' button highlighted][cert_chrome1]

Step 2: Click to proceed to the master.

![Screenshot: Chrome showing an untrusted cert warning, with the 'Proceed to IP address' button highlighted][cert_chrome2]

### Accepting the certificate in Mozilla Firefox

Step 1: When you receive a warning that the connection is untrusted, click **I Understand the Risks**, then click **Add Exception**.

![Screenshot: Firefox's untrusted cert warning, with two controls highlighted][cert_firefox1]

Step 2: Click **Confirm Security Exception**.

![Screenshot: Firefox's cert details dialog, with the confirm button highlighted][cert_firefox2]

### Accepting the certificate in Apple Safari

Click the __Continue__ button on the warning dialog.

![Screenshot: Safari's untrusted cert dialog, with the continue button highlighted][cert_safari]

> **Note**: Safari certificate handling may prevent console access.
>
>Due to [Apache bug 53193](https://issues.apache.org/bugzilla/show_bug.cgi?id=53193) and the way Safari handles certificates, Puppet recommends that PE 2016.1 users avoid using Safari to access the PE console.
>
>If you need to use Safari, you may encounter the following dialog box the first time you attempt to access the console after installing/upgrading PE 2016.1:
>
![Safari Certificate Dialog][client_cert_dialog]
>
>If this happens, click __Cancel__ to access the console. (In some cases, you may need to click __Cancel__ several times.)

### Accepting the certificate in Microsoft Internet Explorer

Click the __Continue to this website (not recommended)__ link on the warning page.

![Screenshot: IE's untrusted cert page, with the continue link highlighted][cert_ie]

[cert_chrome1]: ./images/console/accessing_cert_chrome1.png
[cert_chrome2]: ./images/console/accessing_cert_chrome2.png
[cert_firefox1]: ./images/console/accessing_cert_firefox1.png
[cert_firefox2]: ./images/console/accessing_cert_firefox2.png
[cert_safari]: ./images/console/accessing_cert_safari.png
[cert_ie]: ./images/console/accessing_cert_ie.png
[login]: ./images/console/accessing_login.png
[client_cert_dialog]: ./images/client_cert_dialog.png



Logging in
-----

For security, accessing the console requires a username and password. If you are an administrator setting up the console or accessing it for the first time, use the username and password you chose when you installed the console. Otherwise, get credentials from your site's administrator. See the [user management information](./rbac_user_roles.html#troubleshooting-admin-and-user-access-to-the-console) for more information on managing console user accounts.

Since the console is the main point of control for your infrastructure, you probably want to decline your browser's offer to remember its password.

>**Note**: Resetting the admin password for console access relies on a utility script that's located in the installer tarball. See [Troubleshooting admin and user access to the console](./rbac_user_roles.html#troubleshooting-admin-and-user-access-to-the-console) for steps to reset the admin password.


Changing the length of a console session
-----

By default, a console session times out after one hour. You can change the length of console sessions by [creating a new configuration file](./console_config.html#changing-session-duration), or by performing the following steps:

1. In the console, click **Nodes**, then **Classification**. Select the **PE Console** node group.

2. On the PE Console page, click the **Classes** tab. Under the **puppet_enterprise::profile::console** class, click **Parameter name** and select **rbac_session_timeout**.

3. In the **Value** field, enter the desired length of the console session in minutes. The default value is 60.

4. Click **Add parameter**, then **Commit 1 change**.

In order for this change to take effect, you must initiate a Puppet run, either by [using the **Run Puppet** button in the console](./console_classes_groups_running_puppet.html#run-puppet-on-an-individual-node), or by running `puppet agent -t` on the command line. This Puppet run causes pe-console-services to restart, which also restarts the console.




