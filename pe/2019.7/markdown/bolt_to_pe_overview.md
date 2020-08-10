# Migrating Bolt tasks and plans to PE

If you use Bolt tasks and plans to automate parts of your configuration management, you can move that Bolt content to a control repo and transform it into a PE environment. This lets you manage and run tasks and plans using PE and the console. PE environments and Bolt projects use the same default structure, and both use a Puppetfile to install content.

The control repo is a central Git repository PE fetches content from. An environment is a space for PE authors to write and install content, similar to a Bolt project.

There are two ways to get your Bolt content into an environment:

-   Move your Bolt code to a new control repo. Do this if you have a `Boltdir`, or an [embedded project directory](https://puppet.com/docs/bolt/latest/bolt_project_directories.html#embedded-project-directory), in a repo that also contains other code that you do not wish to migrate to PE.
-   Configure PE to point to the Bolt project. Do this is if you have a dedicated repo for Bolt code, or a [local project directory](https://puppet.com/docs/bolt/latest/bolt_project_directories.html#local-project-directory), and don't want to duplicate it in PE.

## Move Bolt content to a new PE repo

Move your Bolt project content out of your `Boltdir` and into a fresh PE control repo.

-   Install PE on your machine. See [Getting started with Puppet Enterprise](getting_started_pe_overview.md).
-   Set up your PE control repo and environments. See [Managing environments with a control repository](control_repo.md#).

To move Bolt content to a repo:

1.  Commit the contents of your Bolt project to a branch of your PE control repo.

    Your new structure is similar to the [local project directory](https://puppet.com/docs/bolt/latest/bolt_project_directories.html) in Bolt, for example:

    ```
    project/
    ├── Puppetfile
    ├── bolt.yaml
    ├── data
    │   └── common.yaml
    ├── inventory.yaml
    └── site-modules
        └── project
            ├── manifests
            │   └── my_class.pp
            ├── plans
            │   ├── deploy.pp
            │   └── diagnose.pp
            └── tasks
                ├── init.json
                └── init.py
    ```

2.  Create a configuration file called `environment.conf` and add it to the root directory of the branch. This file configures the environment in PE.

3.  Add the `modulepath` setting to the `environment.conf` file by adding the following line:

    ```
    modulepath = modules:site-modules:$basemodulepath
    ```

    **Note:** PE picks up modules only from the `modules` directory. It's important to add `site-modules` to the `modulepath` setting so it matches the defaults for your Bolt project. If you have a `modulepath` setting in `bolt.yaml`, match it to the `modulepath` setting in `environment.conf`.

4.  Publish the branch to the PE control repo.

5.  Deploy code using `puppet code deploy --<ENVIRONMENT>`, where `<ENVIRONMENT>` is the name of your branch, to commit the new branch to Git.

    **Note:** You can also deploy code using a webhook. See [Triggering Code Manager with a webhook](code_mgr_webhook.md#) for more information.


After you deploy code, modules, and the tasks and plans within them, listed in the new environment's Puppetfile will be available to use in PE.

**Related information**  


[Plan caveats and limitations](plans_limitations.md)

## Point PE to a Bolt project

Allow PE to manage content in your dedicated Bolt repo.

-   Install PE on your machine. See [Getting started with Puppet Enterprise](getting_started_pe_overview.md).
-   Ensure your Bolt project follows the [local project directory](https://puppet.com/docs/bolt/latest/bolt_project_directories.html#local-project-directory) structure.

To point PE to your Bolt content:

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

2.  Change the name of your branch to `production`. PE uses branches in Git as environments and the default environment is `production`.

3.  Create a configuration file called `environment.conf` and add it to the root directory of the branch. This file configures the environment.

4.  Add the `modulepath` setting to the `environment.conf` file by adding the following line:

    ```
    modulepath = modules:site-modules:$basemodulepath
    ```

    **Note:** Without this setting, PE picks up modules only from the `modules` directory. It's important to add `site-modules` to the `modulepath` setting so it matches the defaults for your Bolt project. If you have a `modulepath` setting in `bolt.yaml`, match it to the `modulepath` setting in `environment.conf`.

5.  Publish the branch to the PE control repo.

6.  Deploy code using `puppet code deploy --<ENVIRONMENT>`, where `<ENVIRONMENT>` is the name of your branch, to commit the new branch to git.

    **Note:** You can also deploy code using a webhook. See [Triggering Code Manager with a webhook](code_mgr_webhook.md#) for more information.


After you deploy code, modules, and the tasks and plans within them, listed in the new environment's Puppetfile will be available to use in PE.

**Related information**  


[Plan caveats and limitations](plans_limitations.md)

## PE workflows for Bolt users

Review these differences between PE and Bolt commands and workflows before you start running tasks and plans in PE.

### Connecting to nodes

You must connect PE to each node you want to run tasks on or include in a plan. See [Add nodes to the inventory](add_inventory.md#) for instructions on how to add agent or agentless nodes to your inventory.

### Installing tasks and plans

In PE, as in Bolt, you use the `mod` command to download modules. But instead of running the `bolt puppetfile install` command to install them, you trigger Code Manager and deploy code using the `puppet code deploy` command. See [Triggering Code Manager on the command line](puppet_code.md#) for more information.

### Running tasks and plans

PE does not recognize the `bolt` command for running tasks and plans. Instead, use the `puppet task run` and `puppet plan run` commands, or use the console.

To run tasks or plans from the command line, see:

-   [Running tasks from the command line](running_tasks_from_the_command_line.md#)
-   [Running plans from the command line](running_plans_from_the_command_line.md#)

To run tasks or plans from the console, see:

-   [Running tasks from the console](running_tasks_in_the_console.md#)
-   [Running plans from the console](running_plans_from_the_console_.md#)

### Limitations in PE

Not everything in Bolt works in PE. For example, many pre-installed Bolt modules are not included in PEand many plan functions like `file::exists` and `set_feature` do not work. See [Plan caveats and limitations](plans_limitations.md) for more information.

