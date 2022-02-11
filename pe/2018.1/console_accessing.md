# Accessing the console

The console is the web interface for Puppet Enterprise.

Use the console to:

-   Manage node requests to join the Puppet deployment.
-   Assign Puppet classes to nodes and groups.
-   Run Puppet on specific groups of nodes.

-   View reports and activity graphs.
-   Browse and compare resources on your nodes.
-   View package and inventory data.
-   Manage console users and their access privileges.

## Reaching the console

The console is served as a website over SSL, on whichever port you chose when installing the console component.

Let's say your console server is `console.domain.com`. If you chose to use the default port \(443\), you can omit the port from the URL and reach the console by navigating to `https://console.domain.com`.

If you chose to use port 8443, you reach the console at `https://console.domain.com:8443`.

**Remember:** Always use the `https` protocol handler. You cannot reach the console over plain `http`.

## Accepting the console's certificate

The console uses an SSL certificate created by your own local Puppet certificate authority. Since this authority is specific to your site, web browsers won't know it or trust it, and you'll have to add a security exception in order to access the console.

Adding a security exception for the console is safe to do. Your web browser will warn you that the console's identity hasn't been verified by one of the external authorities it knows of, but that doesn't mean it's untrustworthy. Since you or another administrator at your site is in full control of which certificates the Puppet certificate authority signs, the authority verifying the site is *you.*

When your browser warns you that the certificate authority is invalid or unknown:

-   In Chrome, click **Advanced**, then **Proceed to <CONSOLE ADDRESS\>**.

-   In Firefox, click **Advanced**, then **Add exception**.

-   In Internet Explorer or Microsoft Edge, click **Continue to this website \(not recommended\)**.

-   In Safari, click **Continue**.


## Logging in

Accessing the console requires a username and password.

If you are an administrator setting up the console or accessing it for the first time, use the username and password you chose when you installed the console. Otherwise, get credentials from your site's administrator.

Since the console is the main point of control for your infrastructure, you probably want to decline your browser's offer to remember its password.

## Generate a user password reset token

When users forget passwords or lock themselves out of the console by attempting to log in with incorrect credentials too many times, you need to generate a password reset token.

### Procedure

1.  In the console, click Access control \> Users.

2.  Click the name of the user who needs a password reset token.

3.  Click **Generate password reset**. Copy the link provided in the message and send it to the user.


## Reset the admin password

In RBAC, one of the built-in users is the admin, a superuser with all available read/write privileges. To reset the admin password for console access, run the `set_console_admin_password.rb` utility script.

### About this task

### Procedure

1.  Log into the console node as the root user.

    **Note:** You must run the script from the command line of the server installed with the console component. In a split install, it cannot be run from the Puppet master.

2.  Run the `set_console_admin_password.rb` script:

    ```
    /opt/puppetlabs/puppet/bin/ruby /opt/puppetlabs/server/bin/set_console_admin_password.rb <NEW_PASSWORD>
    ```


### Results

### Troubleshooting login to the PE admin account

If your directory contains multiple users with a login name of "admin," the PE admin account is unable to log in.

If you are locked out of PE as the admin user and there are no other users with administrator access who you can ask to reset the access control settings in the console, SSH into the box and use curl commands to reset the directory service settings.

For a box named centos7 the curl call looks like this:

```
curl -X PUT --cert /etc/puppetlabs/puppet/ssl/certs/centos7.pem --key /etc/puppetlabs/puppet/ssl/private_keys/centos7.pem --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem -H "Content-Type: application/json" -d {} https://centos7:4433/rbac-api/v1/ds
```

