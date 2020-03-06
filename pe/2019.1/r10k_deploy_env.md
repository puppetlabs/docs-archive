# Deploying environments with r10k

Deploy environments on the command line with the `r10k` command.

The first time you run r10k, deploy all environments and modules by running `r10k deploy environment`.

This command:

1.  Checks your control repository to see what branches are present.
2.  Maps those branches to the Puppet directory environments.
3.  Clones your Git repo, and either creates \(if this is your first run\) or updates \(if it is a subsequent run\) your directory environments with the contents of your repo branches.

**Note:**

When running commands to deploy code on a master, r10k needs write access to your environment path. Run r10k as the `pe-puppet` user or as root. Running the user as root requires access control to the root user.

## Updating environments

To update environments with r10k, use the ``deploy environment`` command.

### Updating all environments

The `deploy environment` command updates all existing environments and recursively creates new environments.

The `deploy environment` command updates all existing environments and recursively creates new environments. This command updates modules only on the first deployment of a given environment. On subsequent updates, it updates only the environment itself.

From the command line, run `r10k deploy environment`

### Updating all environments and modules

With the `--puppetfile` flag, the `deploy environment` command updates all environments and modules.

This command:

-   Updates all sources.
-   Creates new environments.
-   Deletes old environments.
-   Recursively updates all environment modules specified in each environment's Puppetfile.

This command does the maximum possible work, and is therefore the slowest method for r10k deployments. Usually, use the less resource-intensive commands for updating environments and modules.

From the command line, run `r10k deploy environment --puppetfile`

### Updating a single environment

To updated just one environment, specify that environment name with the `deploy environment` command.

This command updates modules only on the first deployment of the specified environment. On subsequent updates, it updates only the environment itself.

If you're actively developing on a given environment, this is the quickest way to deploy your changes.

On the command line, run `r10k deploy environment <MY_WORKING_ENVIRONMENT>`

### Updating a single environment and its modules

To update both a single given environment and all of the modules contained in that environment's Puppetfile, add the `--puppetfile` flag to your command. This is useful if you want to make sure that a given environment is fully up to date.

On the command line, run `r10k deploy environment <MY_WORKING_ENVIRONMENT> --puppetfile`

## Installing and updating modules

To update modules, use the `r10k deploy module` subcommand. This command installs or updates the modules you've specified in each environment's Puppetfile.

If the specified module is not described in a given environment's Puppetfile, that environment is skipped.

### Updating specific modules across all environments

To update specific modules across all environments, specify the modules with the `deploy module` command.

You can specify a single module or multiple modules. This is useful when you're working on a module and want to update it across all environments.

For example, to update the apache, jenkins, and java modules, run `r10k deploy module apache jenkins java`

### Updating one or more modules in a single environment

To update specific modules in a single environment, specify both the environment and the modules.

On the command line, run `r10k deploy module` with:

-   The environment option `-e`.
-   The name of the environment.
-   The names of the modules you want to update in that environment.

For example, to install the apache, jenkins, and java modules in the production environment, run `r10k deploy module -e production apache jenkins java`

## Viewing environments with r10k

Display information about your environments and modules with the `r10k deploy display` subcommand. This subcommand does not deploy environments, but only displays information about the environments and modules r10k is managing.

This command can return varying levels of detail about the environments.

-   To display all environments being managed by r10k, use the `deploy display` command. From the command line, run `r10k deploy display`

-   To display all managed environments and Puppetfile modules, use the Puppetfile flag, `-p`. From the command line, run `r10k deploy display -p`

-   To get detailed information about module versions, use the `-p` \(Puppetfile\) and `--detail` flags. This provides both the expected and actual versions of the modules listed in each environment's Puppetfile. From the command line, run `r10k deploy display -p --detail`

-   To get detailed information about the modules only in specific environments, limit your query with the environment names. This provides both the expected and actual versions of the modules listed in the Puppetfile in each specified environment. For example, to see details on the modules for environments called production, vmwr, and webrefactor, run:

    ```
    r10k deploy display -p --detail production vmwr webrefactor
    ```


