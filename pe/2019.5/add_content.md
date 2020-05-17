# Add code and set up Code Manager

Set up your control repo, create a Puppetfile, and configure Code Manager so you can start adding content to your PE environments.

The control repo is where you store your code. Code in your control repo is usually bundled in modules.

The Puppetfile specifies detailed information about each environment's Puppet code and data, including where to get that code and data from, where to install it, and whether to update it.

Code Manager automates the management and deployment of your Puppet code. It isn't required to use PE, but it is helpful for ensuring Puppet syncs code to your masters and all your server run new code at the same time.

## Create a control repo from the Puppet template

To create a control repo that includes a standard recommended structure, code examples, and configuration scripts, base your control repo on the Puppet control repo template. This template covers most customer situations.

### About this task

To base your control repo on the Puppet control repository template, you copy the control repo template to your development workstation, set your own remote Git repository as the default source, and then push the template contents to that source.

The control repo template contains the files needed to get you started with a functioning control repo, including:

-   An `environment.conf` file to implement a `site-modules/` directory for roles, profiles, and custom modules.

-   `config_version` scripts to notify you which control repo version was applied to the agents.

-   Basic code examples for setting up roles and profiles.

-   An example `hieradata` directory that matches the default hierarchy .

-   A Puppetfile to manage content maintained in your environment.

### Procedure

1.  Generate a private SSH key to allow access to the control repository.

    This SSH key cannot require a password.

    1.  Generate the key pair:

        ```
        ssh-keygen -t rsa -b 2048 -P '' -f /etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa
        ```

    2.  Set appropriate permissions so that the `pe-puppet` user can access the key:

        ```
        puppet infrastructure configure
        ```

    Your keys are now located in `/etc/puppetlabs/puppetserver/ssh`:

    -   Private key: `/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa`

    -   Public key: `/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa.pub`

    Configure your Git host to use the SSH public key you generated. The process to do this is different for every Git host. Usually, you create a user or service account, and then assign the SSH public key to it.

    Code management needs read access to your control repository, as well as any module repositories referenced in the Puppetfile.

    See the your Git host docs for detailed instructions on adding SSH keys to your Git server. Commonly used Git hosts include:

    -   [GitHub](https://developer.github.com/v3/guides/managing-deploy-keys/#machine-users)

    -   [BitBucket Server](https://confluence.atlassian.com/bitbucketserver/ssh-access-keys-for-system-use-776639781.html)

    -   [GitLab](https://docs.gitlab.com/ce/ssh/README.html#deploy-keys)

2.  Create a repository in your Git account, with the name you want your control repo to have.

    Steps for creating a repository vary, depending on what Git host you are using \( GitHub, GitLab, Bitbucket, or another provider\). See your Git host's documentation for complete instructions.

    For example, on GitHub:

    1.  Click **+** at the top of the page, and choose **New repository**.

    2.  Select the account **Owner** for the repository.

    3.  Name the repository \(for example, `control-repo`\).

    4.  Note the repository's SSH URL for later use.

3.  From the command line, clone the Puppet `control-repo` template.

    ```
    git clone https://github.com/puppetlabs/control-repo.git
    ```

4.  Remove the template repository as your default source.

    ```
    git remote remove origin
    ```

5.  Add the control repository you created as the default source.

    ```
    git remote add origin <URL OF YOUR GIT REPOSITORY>
    ```


### Results

You now have a control repository based on the Puppet`control-repo` template. When you make changes to this repo on your workstation and push those changes to the remote copy of the control repo on your Git server, Code Manager deploys your infrastructure changes.

You also now have a Puppetfile available for you to start adding and managing content, like module code.

## Configure Code Manager

Code Manager stages, commits, and synchronizes your code, automatically managing your environments and modules when you make changes.

### Enable Code Manager

To enable Code Manager set parameters in the console.

#### Before you begin

See the [Configure settings using the console](config_intro.md#) documentation to learn how to set parameters in the console.

#### About this task

#### Procedure

1.  In the console, set the following parameters in the `puppet_enterprise::profile::master` class in the PE Master node group.

    -   `code_manager_auto_configure` to true.
    -   `r10k_remote`: This is the location of your control repository. Enter a string that is a valid URL for your Git control repository. For example: "git@<YOUR.GIT.SERVER.COM\>:puppet/control.git".
    -   `r10k_private_key`: Enter a string specifying the path to the SSH private key that permits the `pe-puppet` user to access your Git repositories. This file must be located on the master, owned by the `pe-puppet` user, and located in a directory that the `pe-puppet` user has permission to view. We recommend `/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa`
2.  On the command line, run `puppet job run --nodes <NODE NAME>` where `<NODE NAME>` is the name of your master.


### Set up authentication for Code Manager

To securely deploy environments, Code Manager needs an authentication token for both authentication and authorization.

#### Before you begin

If needed, configure the Puppet access command line tool. This comes pre-installed on your master. See [Installing PE client tools](installing_pe_client_tools.md#) for more information.

#### About this task

To generate a token for Code Manager, first assign a user to the deployment role, and then request an authentication token.

#### Assign a user to the deployment role

To request an authentication token, you must first assign a user the correct permissions with role-based access control \(RBAC\).

##### Procedure

1.  In the console, create a deployment user. We recommend that you create a dedicated deployment user for Code Manager use.

2.  Add the deployment user to the **Code Deployers** role. This role is automatically created on install, with default permissions for code deployment and token lifetime management.


##### What to do next

Next, request the authentication token.

#### Request an authentication token for deployments

Request an authentication token for the deployment user to enable secure deployment of your code.

##### About this task

By default, authentication tokens have a five-minute lifetime. With the `Override default expiry` permission set, you can change the lifetime of the token to a duration better suited for a long-running, automated process.

Generate the authentication token using the `puppet-access` command.

##### Procedure

1.  From the command line on the master, run `puppet-access login --lifetime 180d`. This command both requests the token and sets the token lifetime to 180 days.

    **Tip:** You can add flags to the request specifying additional settings such as the token file's location or the URL for your RBAC API. See [Configuration file settings for puppet-access](rbac_token_auth_intro.md#).

2.  Enter the username and password of the deployment user when prompted.


##### Results

The generated token is stored in a file for later use. The default location for storing the token is `~/.puppetlabs/token`. To view the token, run `puppet-access show`.

##### What to do next

Next, test the connection to the control repo.

## Deploy your code

Use the command line to trigger Code Manager after making changes to your Puppetfile.

### About this task

When you make changes to your Puppetfile, like adding a new module or creating a repo, you must deploy your code before Code Manager can recognize or start managing the content.

### Procedure

1.  SSH into your master and run `puppet-code deploy --all --wait`.


### Results

You have deployed code to all environments. The `--wait` flag returns results after the deployment is finished. Use the command `puppet-code deploy <ENVIRONMENT>` to deploy code to only a specific environment. You can also deploy code using a [webhook](code_mgr_webhook.md#) or [custom scripts](code_mgr_scripts.md#).

