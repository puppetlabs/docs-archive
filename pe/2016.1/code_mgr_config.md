---
layout: default
title: "Configuring Code Manager"
canonical: "/pe/latest/code_mgr_config.html"
description: "How to configure Code Manager, a system for managing and deploying Puppet code."
---


[puppetfile]: ./cmgmt_puppetfile.html
[code_mgr]: ./code_mgr.html
[r10k]: ./r10k.html
[control_repo]: ./cmgmt_control_repo.html


[code_mgr_config]: ./code_mgr_config.html
[code_mgr_custom]: ./code_mgr_custom.html
[code_mgr_webhook]: ./code_mgr_webhook.html
[scripts]: ./code_mgr_scripts.html
[filesync]: ./cmgmt_filesync.html
[code_mgr_cli]: ./code_mgr_cli.html
[answer_file]: ./install_automated.html

[token]: ./rbac_token_auth.html
[rbac_life]: ./rbac_token_auth.html#changing-the-default-lifetime
[rbac_perm]: ./rbac_permissions.html
[users_roles]: ./rbac_user_roles.html
[create_user]: ./rbac_user_roles.html#create-a-new-user
[config_access]: ./rbac_token_auth.html#configuring-puppet-access
[create_role]: ./rbac_user_roles.html#create-a-new-user-role
[role_perm]: ./rbac_user_roles.html#assign-permissions-to-a-user-role
[add_user]: ./rbac_user_roles.html#add-a-user-to-a-user-role


This page assumes that you have already set up a [control repo][control_repo] and a [Puppetfile][puppetfile]. Additionally, you should have [configured the puppet-access][config_access] command, so that you can generate an authentication token. If so, you're ready to set up Code Manager.

1. Enable and configure Code Manager, either
  * [During a fresh installation](#enable-code-manager-during-installation), by providing answers in the answer file, or
  * [After installation or upgrade](#enable-code-manager-after-installation), by setting parameters in the master profile class in the PE console.
2. [Test your control repo](#test-the-control-repo).
3. [Set up authentication for Code Manager](#set-up-authentication-for-code-manager).
4. [Test Code Manager](#test-code-manager).

After you've completed these steps, you'll be ready to trigger environment deployments with Code Manager via the [command line][code_mgr_cli], a [webhook][code_mgr_webhook], or a [custom script][scripts].

> **Note:** If you enable Code Manager and file sync, do not attempt to follow the workflows in the Puppet Enterprise Quick Start Guides. The `puppet module` command is not currently compatible with Code Manager.

## Enable Code Manager during installation

The easiest way to configure Code Manager is to add answers to the [answer file][answer_file] during a fresh installation. This enables and configures file sync and Code Manager. These answers can only be used with command-line installation using an answer file, not web-based installation.

Note that you must add these answers to the answer file **before** installation.

Provide the following answers in the answers file:

### `q_puppetmaster_r10k_remote`

This setting specifies the location of the control repository. It accepts a string that is a valid URL for your Git control repository. For example:

`q_puppetmaster_r10k_remote='git@<YOUR.GIT.SERVER.COM>:puppet/control.git'`

### `q_puppetmaster_r10k_private_key`

This setting specifies the path to the file that contains the SSH private key used to access all Git repositories. The file must be located on the Puppet master and owned by the `pe-puppet` user.

The setting accepts a string, such as:

`q_puppetmaster_r10k_private_key='/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa'`

> **After** your PE installation is complete, place the private key you created when you [set up your control repository](./cmgmt_control_repo.html#set-up-your-git-repo) in this location. This must be a location that the `pe-puppet` user can access; we recommend `/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa`. The private key file must be located on the Puppet master and owned by the `pe-puppet` user..

### `q_puppetmaster_code_manager_auto_configure`

This setting configures Code Manager using the master profile, along with the Git control repository to use for storing code, and the private key for accessing your Git repos.

Acceptable values are 'y' or 'n'. Set it to 'y' to auto-configure Code Manager. If you set it to 'n', Code Manager and file sync are disabled.

After PE installation is complete, you are ready to proceed to the next step below, [testing the control repo](#test-the-control-repo).

## Enable Code Manager after installation

If you didn't provide answers to the answer file during command line installation, or you are upgrading an existing PE installation, you can enable Code Manager after your installation or upgrade.

### Upgrade notes

If you are upgrading an existing PE installation, check the following before enabling Code Manager:

* If you have a .gitmodules file in the `/etc/puppetlabs/code` directory on any Puppet master, discard it. Code Manager does not support user-modified .gitmodule files.
* If you used r10k prior to PE 2015.3, you might have configured r10k in the PE console using the `pe_r10k` class. If so, you must remove the `pe_r10k` class in the PE console **before** moving to the steps below. These steps will provide a standard Code Manager configuration that you can further customize [in Hiera][code_mgr_custom] as needed.
* If you used any previous versions of r10k, disable any tools that might automatically run r10k. Most commonly, this is the zack-r10k module. Code Manager cannot install or update code properly if other tools are running r10k.

Note that when you start using Code Manager, Code Manager is running r10k in the background. You can no longer directly interact with r10k or use the zack-r10k module.

### Enable Code Manager

1. Enter `chown -R pe-puppet:pe-puppet /etc/puppetlabs/code` on the command line. This ensures that the `pe-puppet` user owns the code directory and can make changes as needed.
2. In the PE Master Node Group in the console, in the `puppet_enterprise::profile::master` class, set the following parameters:
  * `code_manager_auto_configure` to `true`. This enables and automatically configures both Code Manager and file sync.
  * `r10k_remote`: This is the location of your control repository. Enter a string that is a valid URL for your Git control repository. For example: `"git@<YOUR.GIT.SERVER.COM>:puppet/control.git"`.
  * `r10k_private_key`: This is the path to the private key that permits the `pe-puppet` user to access all Git repositories. This file must be owned by the `pe-puppet` user. Enter a string, such as `"/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa"`.
2. Run Puppet on all of your masters.


   If you run Puppet for all your masters at the same time (for example, with **Run Puppet** in the console), you might see errors like this your compile masters' logs:

   ```
   2015-11-20 08:14:38,308 ERROR [clojure-agent-send-off-pool-0]
   [p.e.s.f.file-sync-client-core] File sync failure: Unable to get
   latest-commits from server (https://master.example.com:8140/file-sync/v1/latest-commits).
   java.net.ConnectException: Connection refused
   ```

   You can ignore these errors for now. They occur because Puppet Server is restarting while the compile masters are trying to poll for new code. These errors should stop as soon as the Puppet Server on the master of masters has finished restarting.

After the Puppet run is complete, test the connection to the control repo.

## Test the control repo

Because Code Manager is running r10k in the background, you can test the connection to the control repo with a read-only r10k command. To make sure that Code Manager can connect to the control repo, enter the following on the command line:

`r10k deploy display --fetch`

If the control repo is set up properly, this command fetches and displays a list of the environments in the control repo.

## Set up authentication for Code Manager

Code Manager needs an [authentication token][token] for both authentication and authorization. This token allows Code Manager to securely deploy the requested environment.

These steps assume that you have already [configured the Puppet Access][config_access] command line tool.

To generate a token for Code Manager, first you create a deployment role and user, and then you request an authentication token.


### Create a deployment role and user

Before you request a token, you must assign a user the correct permissions with role-based access control (RBAC). We recommend that you create a dedicated deployment user for Code Manager use. You can find detailed instructions for [creating users and user roles][users_roles] in the RBAC documentation.

To create the deployment user and user role:

1. Create a new role named "Deploy Environments".
2. Assign this role the following permissions:
    * Add the **Puppet Environment** type.
      * Set **Permissions** for this type to **Deploy code**.
      * Set **Object** for this type to **All**.
    * Add the **Tokens** type.
      * Set **Permissions** for this type to **Override default expiry**.
3. Create a deployment user.
4. Add the deployment user to the Deploy Environments role.

Next, request the token.

### Request an authentication token

Note that by default, authentication tokens have a five-minute lifetime. With the **Override default expiry** permission set, you can [change the lifetime][rbac_life] to a duration better suited for a long-running, automated process.

Generate the authentication token using the `puppet-access` command:

1. On the command line on the master, run `puppet-access login --service-url https://<HOSTNAME OF PUPPET ENTERPRISE CONSOLE>:4433/rbac-api --lifetime 180d`.

2. Enter the username and password of the deployment user when prompted.

The generated token is stored in a file for later use. The default location for storing the token is `~/.puppetlabs/token`. To view the token, run `puppet-access show`.

## Test Code Manager

To make sure Code Manager deploys your environments correctly, you should test it. To test, trigger Code Manager on the command line to deploy a single environment.

### Deploy a single environment

To test Code Manager, run the following command to deploy one environment:

`puppet-code deploy my_test_environment --wait`

Check to make sure the environment was deployed. If so, you've set up Code Manager correctly.

## Reference: Code Manager settings

After Code Manager is configured, you can adjust some settings in the master profile in the PE console. These options are required for Code Manager to work, unless otherwise noted.

* `code_manager_auto_configure`: Set to `true` to auto-configure Code Manager.
* `r10k_remote`: The location of the Git control repository. Enter a string that is a valid URL for your Git control repository. For example: `'git@<YOUR.GIT.SERVER.COM>:puppet/control.git'`.
* `r10k_private_key`: Required when using the SSH protocol; optional in all other cases. The file containing the private key used to access all Git repositories. Enter a string, such as '/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa'.
* `r10k_proxy`: Optional. A proxy setting r10k uses when accessing the Forge. If empty, no proxy settings are used.

You can further [customize your Code Manager configuration with Hiera][code_mgr_custom].

## Next steps

When Code Manager is fully enabled and configured, you can trigger Code Manager to deploy your environments:

* Manually, [on the command line][code_mgr_cli].
* Automatically, with a [webhook][code_mgr_webhook].
* Automatically, with a [custom script][scripts] that hits the Code Manager endpoint.
