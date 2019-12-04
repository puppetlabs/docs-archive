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
[pe_conf]: ./install_text_mode.html

[token]: ./rbac_token_auth.html
[rbac_life]: ./rbac_token_auth.html#changing-the-default-lifetime
[rbac_perm]: ./rbac_permissions.html
[create_user]: ./rbac_user_roles.html#create-a-new-user
[config_access]: ./rbac_token_auth.html#working-with-puppet-access
[add_user]: ./rbac_user_roles.html#add-a-user-to-a-user-role
[troubleshoot]: ./code_mgr_troubleshoot.html

If you've prepared your control repository and Puppetfile, you're ready to enable and configure Code Manager.

This page walks you through the steps needed to enable, configure, and test Code Manager. When you're done, you'll be ready to deploy environments with Code Manager via the [command line][code_mgr_cli], a [webhook][code_mgr_webhook], or a [custom script][scripts].

You can enable and configure Code Manager either during or after PE installation.

Configuring Code Manager with a fresh PE installation is the easiest method, but you can do this only during a [text-mode installation](./install_text_mode.html). To enable Code Manager after a new installation or in an existing PE installation, you'll set Code Manager's parameters in the PE console.

1. Enable and configure Code Manager, either
  * [During a fresh installation](#enable-code-manager-during-installation), by setting parameters in `pe.conf`, or
  * [After installation or upgrade](#enable-code-manager-after-installation), by setting parameters in the master profile class in the PE console.
2. [Test your control repo](#test-the-control-repo).
3. [Set up authentication for Code Manager](#set-up-authentication-for-code-manager).
4. [Test Code Manager](#test-code-manager).

> **Note:** If you enable Code Manager and file sync, do not attempt to follow the workflows in the Puppet Enterprise Quick Start Guides. The `puppet module` command is not currently compatible with Code Manager.

Before you begin, you should have a [control repo][control_repo] with a [Puppetfile][puppetfile]. Additionally, you should have configured the [`puppet access`][config_access] command, so that you can generate an authentication token. If so, you’re ready to set up Code Manager.

### Upgrading from r10k to Code Manager

If you are upgrading from r10k to Code Manager, check the following before enabling Code Manager:

* If you used r10k prior to PE 2015.3, you might have configured r10k in the PE console using the `pe_r10k` class. If so, you must remove the `pe_r10k` class in the PE console **before** moving to the steps below. These steps will provide a standard Code Manager configuration that you can further customize [in Hiera][code_mgr_custom] as needed.
* If you used any previous versions of r10k, disable any tools that might automatically run r10k. Most commonly, this is the `zack-r10k` module. Code Manager cannot install or update code properly if other tools are running r10k.

Note that when you start using Code Manager, Code Manager is running r10k in the background. You can no longer directly interact with r10k or use the zack-r10k module.

## Enable Code Manager during installation

To configure Code Manager during a fresh installation, add parameters to the [pe.conf file][pe_conf]. Use these parameters **only** with [text-mode installation](./install_text_mode.html), not with web-based installation. Adding the listed parameters enables and configures file sync and Code Manager. 

1. Add the following three parameters to `pe.conf` **before** installation.

  * `puppet_enterprise::profile::master::r10k_remote`

  This setting specifies the location of the control repository. It accepts a string that is a valid URL for your Git control repository. For example:

  ```
"puppet_enterprise::profile::master::r10k_remote": "git@<YOUR.GIT.SERVER.COM>:puppet/control.git"
  ```

  * `puppet_enterprise::profile::master::r10k_private_key`

  This setting specifies the path to the file that contains the SSH private key used to access your Git repositories. The `pe-puppet` user *must* be able to access this location; we recommend `/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa`.
  
  This SSH private key file, which you created when you [set up your control repository](./cmgmt_control_repo.html#set-up-your-git-repo), must be located on the Puppet master and owned by the `pe-puppet` user. **After** PE installation is completed, place the key in the specified location. You'll do this in step 3 below.

  The setting accepts a string, such as:

  ```
"puppet_enterprise::profile::master::r10k_private_key": "/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa"
  ```

* `puppet_enterprise::profile::master::code_manager_auto_configure`

  This setting configures Code Manager, the Git control repository to use for storing code, and the private key for accessing your Git repos.

  Acceptable values are `true` or `false` (case sensitive). Set it to `true` to enable and configure Code Manager. If you set it to `false`, Code Manager and file sync are disabled.

2. Complete PE installation.

3. After PE installation is complete, place the SSH private key you created when you [set up your control repository](./cmgmt_control_repo.html#set-up-your-git-repo) in the `r10k_private_key` location.

Configuration is now complete. Next, [set up authentication for Code Manager](#set-up-authentication-for-code-manager).

## Enable Code Manager after installation

To enable Code Manager after installing PE or in an existing PE installation, set parameters in the PE console.

1. In the console, set the following parameters in the `puppet_enterprise::profile::master` class in the PE Master node group:
  * `code_manager_auto_configure` to `true`. This enables and configures both Code Manager and file sync.
  * `r10k_remote`: This is the location of your control repository. Enter a string that is a valid URL for your Git control repository. For example: `"git@<YOUR.GIT.SERVER.COM>:puppet/control.git"`.
  * `r10k_private_key`: This is the path to the private key that permits the `pe-puppet` user to access your Git repositories. This file must be located on the Puppet master, owned by the `pe-puppet` user, and in a directory that the `pe-puppet` user has permission to view. We recommend `/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa`. Enter a string, such as `"/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa"`.
2. [Run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes) on all of your masters.

   If you run Puppet for all your masters at the same time, such as with the **Run Puppet** button in the console, you might see errors like this your compile masters' logs:

   ```
   2015-11-20 08:14:38,308 ERROR [clojure-agent-send-off-pool-0]
   [p.e.s.f.file-sync-client-core] File sync failure: Unable to get
   latest-commits from server (https://master.example.com:8140/file-sync/v1/latest-commits).
   java.net.ConnectException: Connection refused
   ```

   You can ignore these errors. They occur because Puppet Server is restarting while the compile masters are trying to poll for new code. These errors should stop as soon as the Puppet Server on the master of masters has finished restarting.

After the Puppet run is complete, set up authentication.

## Set up authentication for Code Manager

Code Manager needs an [authentication token][token] for both authentication and authorization. This token allows Code Manager to securely deploy the requested environment.

To generate a token for Code Manager, you'll first assign a user to the deployment role, and then request an authentication token.

Before you begin, you must [configure the Puppet access][config_access] command line tool. 

### Assign a user to the deployment role

Before you request a token, you must assign a user the correct permissions with role-based access control (RBAC).

3. [Create a deployment user][create_user]. We recommend that you create a dedicated deployment user for Code Manager use.
4. [Add the deployment user][add_user] to the Code Deployers role. This role is automatically created on install, with default permissions for code deployment and token lifetime management.

Next, request the token.

### Request an authentication token

Note that by default, authentication tokens have a five-minute lifetime. With the **Override default expiry** permission set, you can [change the lifetime][rbac_life] to a duration better suited for a long-running, automated process.

Generate the authentication token using the `puppet-access` command:

1. On the command line on the master, run `puppet-access login --service-url https://<HOSTNAME OF PUPPET ENTERPRISE CONSOLE>:4433/rbac-api --lifetime 180d`.

2. Enter the username and password of the deployment user when prompted.

The generated token is stored in a file for later use. The default location for storing the token is `~/.puppetlabs/token`. To view the token, run `puppet-access show`.

Next, test the connection to the control repo.

## Test the control repo

To make sure that Code Manager can connect to the control repo, test the connection.

1. Enter the following on the command line:

   `puppet-code deploy --dry-run`

   If the control repo is set up properly, this command fetches and displays a list of the environments in the control repo.
   
   **Note:** If an environment is not set up properly or causes an error, it will not appear in the returned list. 

## Test Code Manager

To make sure Code Manager deploys your environments correctly, you should test it. To test, trigger Code Manager on the command line to deploy a single environment.

### Deploy a single environment

1. Run the following command to deploy one environment:

`puppet-code deploy my_test_environment --wait`

This command deploys the test environment, and then returns deployment results with the SHA (a checksum for the content stored) for the control repo commit. Check to make sure the environment was deployed. If so, you've set up Code Manager correctly.

If the deployment does not work as you expect, check over the configuration steps, or refer to the [troubleshooting][troubleshoot] guide for help.

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