---
layout: default
title: "Setting Up Code Manager with a PE Upgrade"
canonical: "/pe/latest/code_mgr_upgrade.html"
description: "How to configure Code Manager with a Puppet Enterprise upgrade."
---

[repo]: ./cmgmt_control_repo.html
[puppetfile]: ./cmgmt_puppetfile.html
[code_mgr]: ./code_mgr.html
[r10k]: ./r10k.html
[code_mgr_config]: ./code_mgr_config.html
[code_mgr_custom]: ./code_mgr_custom.html
[code_mgr_webhook]: ./code_mgr_webhook.html
[scripts]: ./code_mgr_scripts.html
[r10k_config]: ./r10k_config.html
[r10k_custom]: ./r10k_custom.html
[r10k_run]: ./r10k_run.html
[r10k_ref]: ./r10k_ref.html
[staging]: ./code_mgr.html#understanding-file-sync-and-the-staging-directory
[filesync]: ./cmgmt_filesync.html

[token]: ./rbac_token_auth.html
[rbac_life]: ./rbac_token_auth.html#changing-the-default-lifetime
[rbac_perm]: ./rbac_permissions.html
[users_roles]: ./rbac_user_roles.html
[create_user]: ./rbac_user_roles.html#create-a-new-user
[config_access]: ./rbac_token_auth.html#configuring-puppet-access
[role_perm]: ./rbac_user_roles.html#assign-permissions-to-a-user-role
[add_user]: ./rbac_user_roles.html#add-a-user-to-a-user-role

If you're moving to Code Manager after upgrading an existing PE installation, your configuration steps for Code Manager are slightly different than for a fresh install.

1. [Test the control repo](#test-the-control-repo).
2. [Enable file sync](#enable-file-sync).
3. [Enable Code Manager](#enable-code-manager).
4. [Test Code Manager](#test-code-manager).
5. [Deploy your environments](#deploy-environments).

After you've completed these steps, you'll be ready to set up a deployment trigger for Code Manager with either a [webhook][code_mgr_webhook] or a [custom script][scripts].

## Test the Control Repo

> #### A note to r10k users
>
>If you used r10k prior to PE 2015.3, you might have configured r10k in the PE console using the `pe_r10k` class. If so, you'll need to remove the `pe_r10k` class in the PE console **before** moving to step 2 below. You can further customize your configuration [in Hiera][r10k_custom] as needed.
>
>If you were using earlier versions of r10k with the zack-r10k module, we encourage you to switch to the master profile configuration as described here.


1. If you haven't already done so, [create a control repository][repo] and any necessary [Puppetfiles][puppetfile]. Place your code, including Puppetfiles and any modules that you have written, into your control repository.
2. In the PE console, in the `puppet_enterprise::profile::master` class, set these two r10k parameters: 
  * `r10k_remote`: This is the location of your Git control repository. Accepts a string that is a valid URL for your Git remote. For example: `'git@<YOUR.GIT.SERVER.COM>:puppet/control.git'`.
  * `r10k_private_key`: This is the path to the private key that permits the `pe-puppet` user to access all Git repositories. This file must be owned by the `pe-puppet` user. Accepts a string, such as `'/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa'`.
3. Run Puppet to finish configuring the r10k parameters.
4. Test r10k by running it at the command line. This command deploys only the specified environment, along with the modules designated in the environment's Puppetfile:

    `r10k deploy environment my_test_environment`

5. Make sure my_test_environment has been created inside `/etc/puppetlabs/code/environments`. If so, the control repo is functioning correctly.

## Enable File Sync

> **Note:** If you enable file sync, you should not attempt to follow the workflows in the Puppet Enterprise Quick Start Guides.

1. Disable any tools that might automatically run r10k. Most commonly, this is the zack-r10k module. Code management tools cannot install or update code properly if other tools are running r10k. Note that when you start using Code Manager, you can no longer directly interact with r10k.
2. In the `puppet_enterprise::profile::master` class, set `file_sync_enabled` to `true`. 
3. Run Puppet on all of your masters, so that file sync can create the files and directories it needs, including the [staging directory][staging].

    If you run Puppet for all your masters at the same time (for example, with the PE console **Run Puppet** button), you might see errors like this your compile masters' logs:

    
        2015-11-20 08:14:38,308 ERROR [clojure-agent-send-off-pool-0]
        [p.e.s.f.file-sync-client-core] File sync failure: Unable to get
        latest-commits from server (https://master.example.com:8140/file-sync/v1/latest-commits). 
        java.net.ConnectException: Connection refused
    
    You can ignore these errors for now. They occur because Puppet Server is restarting while the compile masters are trying to poll for new code. These errors should stop as soon as the Puppet Server on the master of masters (MoM) has finished restarting.
4. On your masters, empty the live code directory at `/etc/puppetlabs/code` as follows:
  * On the MoM, **move** the code, including hidden files, from `/etc/puppetlabs/code` to the new staging directory (`/etc/puppetlabs/code-staging`). If you have a .gitmodules file in `/etc/puppetlabs/code`, **do not move this file** to the staging directory, but discard it. File sync does not support user-modified .gitmodule files.
  
        The live code directory on the MoM must still exist and be owned by `pe-puppet:pe-puppet`, but it should be empty of any content. 
    
  * On your compile masters, remove all code, including hidden files, from the live code directory.
  
> The `/etc/puppetlabs/code` directory on your masters must be empty before file sync runs for the first time to prevent crashes.
   
## Enable Code Manager

1. In the `puppet_enterprise::profile::master` class, set `code_manager_auto_configure` to `true`. This both enables and automatically configures Code Manager.
2. Run Puppet on your MoM. You do not need to run Puppet on your compile masters for this step.

## Test Code Manager

To make sure Code Manager deploys your environments correctly, you should test it. To test, you'll run a curl command that triggers Code Manager to deploy a single environment. 

1. [Request an authentication token](#request-an-authentication-token) to authorize Code Manager to deploy code.
2. [Create a custom URL](#create-a-custom-url) to use to trigger Code Manager.
3. [Run a test curl statement](#deploy-a-single-environment) to deploy a single environment.

You can use this same authentication token and custom URL when you set up your automatic Code Manager trigger with a [webhook][code_mgr_webhook] or [custom script][scripts].

### Generate an authentication Token

Code Manager needs an [authentication token][token] to use for both authentication and authorization. This token allows Code Manager to securely deploy the requested environment.

To generate a token for Code Manager:

1. [Create a deployment user role and user](#create-a-deployment-role-and-user).
2. [Request an authentication token](#request-an-authentication-token) for the deployment user. This step requires that you have already [configured the Puppet Access][config_access] command line tool.

#### Create a deployment role and user

Before you request a token, you must assign a user the correct permissions with role-based access control (RBAC). We recommend that you create a dedicated deployment user for Code Manager use. You can find detailed instructions for [creating users and user roles][users_roles] in the RBAC documentation.

To create the deployment user and user role:

1. [Create a new role][create_user] named "Deploy Environments".
2. [Assign this role][role_perm] the following permissions:
    * Add the **Puppet Environment** type.
      * Set **Permissions** for this type to **Deploy code**.
      * Set **Object** for this type to **All**.
    * Add the **Tokens** type.
      * Set **Permissions** for this type to **Override default expiry**.
3. [Create a deployment user][create_user].
4. [Add the deployment user][add_user] to the Deploy Environments role.

Next, request the token.

#### Request an authentication token

To request this token, you must have already [configured the Puppet Access][config_access] command line tool. 

Note that by default, authentication tokens have a five-minute lifetime. With the **Override default expiry** permission set, you can [change the lifetime][rbac_life] to a duration better suited for a long-running, automated process.

Generate the authentication token using the `puppet-access` command:

1. On the command line on the master, run `puppet-access login --service-url https://<HOSTNAME OF PUPPET ENTERPRISE CONSOLE>:4433/rbac-api --lifetime 180d`. 

2. Enter the username and password of the deployment user when prompted.
   
The generated token is stored in a file for later use. The default location for storing the token is `~/.puppetlabs/token`. To view the token, run `puppet-access show`.

In the next step, you'll attach this token to a custom URL.

### Create a custom URL

To trigger Code Manager, you’ll need to use a custom URL. The URL is composed of:

* Your Puppet master server name (for example, `localhost`)
* The Code Manager port (for example, 8170)
* The endpoint (`/code-manager/v1/deploys/`)
* The authentication token you generated earlier (`token=<TOKEN>`)

This URL might look something like this:

```
https://localhost:8170/code-manager/v1/deploys?token=`cat ~/.puppetlabs/token`
```

### Deploy a single environment

   To test Code Manager, run a curl statement that deploys one environment:
   
        curl -k -X POST -H 'Content-Type: application/json' "https://localhost:8170/code-manager/v1/deploys?token=`cat ~/.puppetlabs/token`" -d '{"environments": ["my_test_environment"], "wait": true}'

Check to make sure the environment was deployed.

## Deploy environments

After you have confirmed that Code Manager is configured correctly, you are ready to populate your staging directory and deploy all your environments. To do this, run the curl statement:

        curl -k -X POST -H 'Content-Type: application/json' "https://localhost:8170/code-manager/v1/deploys?token=`cat ~/.puppetlabs/token`" -d '{"deploy-all": true}'

After Code Manager has finished its run, your environments are deployed and your staging directory is populated and ready for use.

### Changing Code Manager's configuration

After Code Manager is configured, you can adjust some settings in the master profile in the PE console. These options are required for Code Manager to work, unless otherwise noted.

* `code_manager_auto_configure`: Set to `true` to auto-configure Code Manager.
* `r10k_remote`: The location of the Git control repository. Accepts a string that is a valid URL for your Git remote. For example: `'git@<YOUR.GIT.SERVER.COM>:puppet/control.git'`.
* `r10k_private_key`: Required when using the SSH protocol; optional in all other cases. The file containing the private key used to access all Git repositories. Accepts a string, such as '/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa'.
* `r10k_proxy`: Optional. A proxy setting r10k uses when accessing the Forge. If empty, no proxy settings are used.

If necessary, you can further [customize your Code Manager configuration with Hiera][code_mgr_custom].
 
## Next steps

Now that Code Manager is fully enabled and configured, set up an automatic deployment trigger for Code Manager with either a [webhook][code_mgr_webhook] or a [custom script][scripts].
