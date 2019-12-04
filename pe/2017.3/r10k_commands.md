---
author: Jean Bond <jean@puppet.com\>
---

# r10k command reference

r10k accepts several command line actions, with options and subcommands.

## r10k command actions

r10k includes several command line actions.

|`r10k` command|Action|
|--------------|------|
|`deploy`|Performs operations on environments. Accepts `deploy` subcommands listed below.|
|`help`|Displays the r10k help page in the terminal.|
|`puppetfile`|Performs operations on a Puppetfile. Accepts `puppetfile` subcommands.|
|`version`|Displays your r10k version in the terminal.|

```
r10k deploy
```

### r10k command options

`r10k` commands accept the following options.

|`r10k` command options|Action|
|----------------------|------|
|`--color`|Enables color coded log messages.|
|`--help`, `-h`|Shows help for this command.|
|`--trace`, `-t`|Displays stack traces on application crash.|
|`--verbose`, `-v`|Sets log verbosity. Valid values: fatal, error, warn, notice, info, debug, debug1, debug2.|

## r10k deploy subcommands

You can use the following subcommands with the `r10k deploy` command.

```
r10k deploy --display
```

|`deploy` subcommand|Action|
|-------------------|:----:|
|`display`|Displays a list of environments in your deployment.|
|`display -p`|Displays a list of modules in the Puppetfile.|
|`display --detail`|Displays detailed information.|
|`display --fetch`|Update the environment sources. Allows you to check for missing environments.|
|`environment`|Deploys environments and their specified modules.|
|`environment -p`|Updates modules specified in the environment's Puppetfile.|
|`module`|Deploys a module in all environments.|
|`module -e`|Updates all modules in a particular environment.|
|`no-force`|Prevents the overwriting of local changes to Git-based modules.|

See the related topic about deploying environments with r10k for examples of `r10k deploy` command use.

## r10k puppetfile subcommands

The `r10k puppetfile` command accepts several subcommands for managing modules.

```
r10k puppetfile install
```

These subcommands must be run as the user with write access to that environment’s modules directory. These commands interact with the Puppetfile in the current working directory, so before running the subcommand, make sure you are in the directory of the Puppetfile you want to use.

|`puppetfile` subcommand|Action|
|-----------------------|:----:|
|`check`|Verifies the Puppetfile syntax is correct.|
|`install`|Installs all modules from a Puppetfile.|
|`install --puppetfile`|Installs modules from a custom Puppetfile path.|
|`install --moduledir`|Installs modules from a Puppetfile to a custom module directory location.|
|`install --force`|Installs modules from a Puppetfile with a forced overwrite of local changes to Git-based modules.|
|`purge`|Purges unmanaged modules from a Puppetfile managed directory.|

