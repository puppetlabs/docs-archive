# Managing environments with a control repository

To manage your Puppet code and data with either Code Manager or r10k, you need a Git version control repository. This control repository is where code management stores code and data to deploy your environments.

## How the control repository works

Code management relies on version control to track, maintain, and deploy your Puppet code and data. The control repository \(or repo\) is the Git repository that code management uses to manage environments in your infrastructure. As you update code and data in your control repo, code management keeps each of your environments updated.

Code management creates and maintains your environments based on the branches in your control repo. For example, if your control repo has a production branch, a development branch, and a testing branch, code management creates a production environment, a development environment, and a testing environment, each with its own version of your Puppet code and data.

Environments are created in `/etc/puppetlabs/code/environments` on the master. To learn more about environments in Puppet, read the documentation about [environments](https://docs.puppet.com/puppet/5.3/environments_about.html).

To create a control repo that includes the standard recommended structure, code examples, and configuration scripts, base your control repo on the Puppet control repo template. This template covers most customer situations. If you cannot access the internet or cannot use modules directly from the Forge because of your organization's security rules, create an empty control repo and add the files you need to it.

**Note:** For Windows systems, be sure your version control is configured to use CRLF line endings. See your version control system for instructions on how to do this.

At minimum, a control repo comprises:

-   A Git remote repository. The remote is where your control repo is stored on your Git host.

-   A default branch named `production`, rather than the usual Git default of `master`.

-   A Puppetfile to manage your environment content.

-   An `environment.conf` file that modifies the `$modulepath` setting to allow environment-specific modules and settings.


CAUTION:

Enabling code management means that Puppet manages the environment directories and **existing environments are not preserved**. Environments with the same name as the new one are overwritten. Environments not represented in the control repo are erased. If you were using environments before, commit any necessary files or code to the appropriate new control repo branch, or back them up somewhere *before* you start configuring code management.

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

## Create an empty control repo

If you can't use the control repo template because, for example, you cannot access the internet or use modules directly from the Forge because of your security rules, create an empty control repo and then add the files you need. 

### About this task

To start with an empty control repo, you create a new repo on your Git host and then copy it to your workstation. You make some changes to your repo, including adding a configuration file that allows code management tools to find modules in both your site and environment-specific module directories. When you're done making changes, push your changes to your repository on your Git host. 

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

3.  Clone the empty repository to your workstation by running `git clone <REPOSITORY URL>`

4.  Create a file named `environment.conf` in the main directory of your control repo.

5.  In your text editor, open `environment.conf` file, and add the line below to set the modulepath. Save and close the file.

    ```
    modulepath=site-modules:modules:$basemodulepath
    ```

6.  Add and commit your change to the repository by running:

    1.  `git add environment.conf`

    2.  `git commit -m "add environment.conf"`

    The `environment.conf` file allows code management tools to find modules in both your site and environment-specific module directories. For details about this file, see the [environment.conf](https://puppet.com/docs/puppet/6.10/config_file_environment.html) documentation.

7.  Rename the `master` branch to `production` by running `git branch -m master production`

    **Important:** The default branch of a control repo must be `production`.

8.  Push your repository's `production` branch from your workstation to your Git host by running `git push -u origin production`


### Results

When you make changes to this repo on your workstation, push those changes to the remote copy of the control repo on your Git server, so that code management can deploy your infrastructure changes.

### What to do next

After you've set up your control repo, create a Puppetfile for managing your environment content with code management.

If you already have a Puppetfile, you can now configure code management. Code management configuration steps differ, depending on whether you're using Code Manager \(recommended\) or r10k. For important information about the function and limitations of each of these tools, along with configuration instructions, see the Code Manager and r10k pages.

## Add an environment

Create new environments by creating branches based on the production branch of your control repository.

### Before you begin

Make sure you have:

-   Configured either [Code Manager](code_mgr_config.md#) or [r10k](r10k_config.md#).

-   Created a [Puppetfile](puppetfile.md#) in the default \(usually 'production'\) branch of your control repo.

-   Selected your code management deployment method \(such as the `[puppet-code](puppet_code.md#)` command or a [webhook](code_mgr_webhook.md#)\).


### About this task

**Remember:** If you are using multiple control repos, do not duplicate branch names unless you use a source prefix. For more information about source prefixes, see the documentation about configuring [sources](code_mgr_customizing.md#).

### Procedure

1.  Create a new branch: `git branch <NAME-OF-NEW-BRANCH>`

2.  Check out the new branch: `git checkout <NAME-OF-NEW-BRANCH>`

3.  Edit the Puppetfile to track the modules and data needed in your new environment, and save your changes.

4.  Commit your changes: `git commit -m "a commit message summarizing your change"`

5.  Push your changes: `git push origin <NAME-OF-NEW-BRANCH>`

6.  Deploy your environments as you normally would, either on the command line or with a Code Manager webhook.


## Delete an environment with code management

To delete an environment with Code Manager or r10k, delete the corresponding branch from your control repository.

### Procedure

1.  On the production branch of your control repo directory, on the command line, delete the environment's corresponding remote branch by running `git push origin --delete <BRANCH-TO-DELETE>`

2.  Delete the local branch by running `git branch -d <BRANCH-TO-DELETE>`

3.  Deploy your environments as you normally would, either on the command line or with a Code Manager webhook.


### Results

**Note:** If you use Code Manager to deploy environments with the webhook, deleting a branch from your control repository does not immediately delete that environment from the master's live code directories. Code Manager deletes the environment when it next deploys changes to any other environment. Alternately, to delete the environment immediately, deploy all environments manually, run `puppet-code deploy --all --wait`

### What to do next

