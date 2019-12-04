# Configuring Code Manager

To configure Code Manager, you'll enable it in Puppet Enterprise \(PE\), set up authentication, and test the communication between the control repo and Code Manager.

When you've finished configuration, you'll be ready to deploy environments with Code Manager.

You can enable and configure Code Manager either during or after PE installation.

Configuring Code Manager with a fresh PE installation is the easiest method, but you can do this only during a text-mode installation. To enable Code Manager after a new installation or in an existing PE installation, you'll set Code Manager parameters in the console.

1.  Enable and configure Code Manager, either during a fresh installation, by setting parameters in `pe.conf`, or after installation or upgrade, by setting parameters in the master profile class in the PE console.
2.  Test your control repo.

3.  Set up authentication for Code Manager.

4.  Test Code Manager.


**Important:** If you enable Code Manager, do not attempt to follow the workflows in the PE getting started guides. The `puppet module` command is not compatible with Code Manager.

## Upgrading from r10k to Code Manager

If you are upgrading from r10k to Code Manager, be sure you know how to disable your old r10k installation.

If you are upgrading from r10k to Code Manager, check the following before enabling Code Manager:

-   If you used r10k prior to PE 2015.3, you might have configured r10k in the console using the `pe_r10k` class. If so, you must remove the `pe_r10k` class in the console **before** configuring Code Manager.
-   If you used any previous versions of r10k, disable any tools that might automatically run it. Most commonly, this is the ``zack-r10k`` module. Code Manager cannot install or update code properly if other tools are running r10k.

When you start using Code Manager, it runs r10k in the background. You can no longer directly interact with r10k or use the `zack-r10k` module.

## Enable Code Manager

You can enable Code Manager either during or after a Code Manager installation.

### About this task

### Enable Code Manager during installation

To configure Code Manager during a fresh installation of PE, add parameters to the `pe.conf` file.

#### About this task

Use these parameters **only** with text-mode installation of PE, not with web-based installation. Adding the listed parameters enables and configures file sync and Code Manager.

#### Procedure

1.  Add the following three parameters to `pe.conf` *before* installation.

    -   `puppet_enterprise::profile::master::r10k_remote`

        This setting specifies the location of the control repository. It accepts a string that is a valid URL for your Git control repository. For example:

        ```
        `"puppet_enterprise::profile::master::r10k_remote": "git@<YOUR.GIT.SERVER.COM>:puppet/control.git"`
                                
        ```

    -   `puppet_enterprise::profile::master::r10k_private_key`

        This setting specifies the path to the file that contains the SSH private key used to access your Git repositories. The `pe-puppet` user **must** be able to access this location; we recommend `/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa`.

        This SSH private key file, which you created when you set up your control repository, must be located on the Puppet master and owned by the `pe-puppet` user. **After**PE installation is completed, place the key in the specified location. You'll do this in step 3 below.

        The setting accepts a string, such as

        ```
        "puppet_enterprise::profile::master::r10k_private_key": "/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa"
        ```

    -   `puppet_enterprise::profile::master::code_manager_auto_configure`

        This setting configures Code Manager, the Git control repository to use for storing code, and the private key for accessing your Git repos. Acceptable values are `true` and `false` \(case sensitive\). Set it to `true` to enable and configure Code Manager. If you set it to `false`, Code Manager and file sync are disabled.

2.  Complete PE installation.

3.  After PE installation is complete, place the SSH private key you created when you set up your control repository in the `r10k_private_key` location.


#### What to do next

Next, set up authentication.

**Related topics**  


[Install using text mode \(mono configuration\)](installing_pe.md#)

[Configuration parameters and the pe.conf file](installing_pe.md#)

### Enable Code Manager after installation

To enable Code Manager after installing PE or in an existing installation, set parameters in the console.

#### Procedure

1.  In the console, set the following parameters in the `puppet_enterprise::profile::master` class in the PE Master node group.

    -   `code_manager_auto_configure` to true: This enables and configures both Code Manager and file sync.
    -   `r10k_remote`: This is the location of your control repository. Enter a string that is a valid URL for your Git control repository. For example: "git@<YOUR.GIT.SERVER.COM\>:puppet/control.git".

        **Note:** For Bitbucket, you must preface your remote URL with `ssh://` , to match the format of Bitbucket’s SSH URLs. For example, `ssh://git@githost.domain.com/pup/puppet.git`.

    -   `r10k_private_key`: Enter a string specifying the path to the SSH private key that permits the `pe-puppet` user to access your Git repositories. This file must be located on the master, owned by the `pe-puppet` user, and located in a directory that the `pe-puppet` user has permission to view. We recommend `/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa`.
2.  Run Puppet on all masters.

    If you run Puppet for all your masters at the same time, such as with **Run Puppet** in the console, you might see errors like this your compile masters' logs:

    ```
    2015-11-20 08:14:38,308 ERROR [clojure-agent-send-off-pool-0]
    [p.e.s.f.file-sync-client-core] File sync failure: Unable to get
    latest-commits from server (https://master.example.com:8140/file-sync/v1/latest-commits).
    java.net.ConnectException: Connection refused
    
    ```

    You can ignore these errors. They occur because Puppet Server is restarting while the compile masters are trying to poll for new code. These errors should stop as soon as the Puppet Server on the master of masters has finished restarting.


#### Results

#### What to do next

Next, set up authentication.

## Set up authentication for Code Manager

To securely deploy environments, Code Manager needs an authentication token for both authentication and authorization.

### Before you begin

Configure the Puppet access command line tool.

### About this task

To generate a token for Code Manager, you'll first assign a user to the deployment role, and then request an authentication token.

**Related topics**  


[Configuring puppet-access](rbac_token_auth_intro.md#)

### Assign a user to the deployment role

To request an authentication token, you must first assign a user the correct permissions with role-based access control \(RBAC\).

#### Procedure

1.  In the console, create a deployment user. We recommend that you create a dedicated deployment user for Code Manager use.

2.  Add the deployment user to the **Code Deployers** role. This role is automatically created on install, with default permissions for code deployment and token lifetime management.


#### What to do next

Next, request the authentication token.

**Related topics**  


[Add a user to a user role](rbac_user_roles_intro.md#)

[Assign a user group to a user role](rbac_user_roles_user_groups_ex_dir.md#)

### Request an authentication token for deployments

Request an authentication token for the deployment user to enable secure deployment of your code.

#### About this task

By default, authentication tokens have a five-minute lifetime. With the `Override default expiry` permission set, you can change the lifetime of the token to a duration better suited for a long-running, automated process.

Generate the authentication token using the `puppet-access` command.

#### Procedure

1.  From the command line on the master, run `puppet-access login --lifetime 180d`. This command both requests the token and sets the token lifetime to 180 days.

    **Tip:** You can add flags to the request specifying additional settings such as the token file's location or the URL for your RBAC API. See [Configuration file settings for puppet-access](rbac_token_auth_intro.md#).

2.  Enter the username and password of the deployment user when prompted.


#### Results

The generated token is stored in a file for later use. The default location for storing the token is `~/.puppetlabs/token`. To view the token, run `puppet-access show`. Next, test the connection to the control repo.

**Related topics**  


[Setting a token-specific lifetime](rbac_token_auth_intro.md#)

[Generate a token for use by a service](rbac_token_auth_intro.md#)

## Test the control repo

To make sure that Code Manager can connect to the control repo, test the connection to the repository.

### Procedure

1.  From the command line, run `puppet-code deploy --dry-run`


### Results

If the control repo is set up properly, this command fetches and displays a list of the environments in the control repo.

If an environment is not set up properly or causes an error, it will not appear in the returned list.

## Test Code Manager

To test whether Code Manager deploys your environments correctly, trigger a single environment deployment on the command line.

### Deploy a single environment

Test Code Manager by deploying a single test environment.

#### About this task

This deploys the test environment, and then returns deployment results with the SHA \(a checksum for the content stored\) for the control repo commit.

#### Procedure

1.  From the command line, deploy one environment by running `puppet-code deploy my_test_environment --wait`

    Check to make sure the environment was deployed. If so, you've set up Code Manager correctly.

    If the deployment does not work as you expect, check over the configuration steps, or refer to the troubleshooting guide for help.


#### Results

After Code Manager is fully enabled and configured, you can trigger it to deploy your environments.

There are several ways to trigger deployments, depending on your needs.

-   Manually, on the command line.
-   Automatically, with a webhook.
-   Automatically, with a custom script that hits the deploys endpoint.

## Code Manager console settings

After Code Manager is configured, you can adjust some settings in the master profile in the console.

These options are required for Code Manager to work, unless otherwise noted.

|Setting|Description|Example|
|-------|:---------:|:------|
|`code_manager_auto_configure`|Set to true to auto-configure Code Manager.|`true`|
|`r10k_remote`|The location of the Git control repository. Enter a string that is a valid URL for your control repository.|`'git@<YOUR.GIT.SERVER.COM>:puppet/control.git'`|
|`r10k_private_key`|Required when using the SSH protocol; optional in all other cases. Enter a string that is the path to the file containing the private key used to access all Git repositories.|`'/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa'`|
|`r10k_proxy`|Optional. A proxy setting r10kCode Manager uses when accessing the Forge. If empty, no proxy settings are used.|`'http://proxy.example.com:3128'`|

To further customize your Code Manager configuration with Hiera, see the related topic about customizing your configuration.

