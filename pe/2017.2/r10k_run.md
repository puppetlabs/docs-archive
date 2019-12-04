---
layout: default
title: "Deploying environments with r10k"
canonical: "/pe/latest/r10k_run.html"
description: "Deploying environments and modules with r10k, a Puppet code management tool."
---

[environ_dir]: {{puppet}}/environments_configuring.html
[puppetfile]: ./cmgmt_puppetfile.html
[run]: ./r10k_run.html
[reference]: ./r10k_reference.html
[repo]: ./cmgmt_control_repo.html
[puppetfile]: ./cmgmt_puppetfile.html
[code_mgr]: ./code_mgr.html
[r10k]: ./r10k.html
[code_mgr_config]: ./code_mgr_config.html
[code_mgr_custom]: ./code_mgr_custom.html
[code_mgr_webhook]: ./code_mgr_webhook.html
[r10k_config]: ./r10k_config.html
[r10k_custom]: ./r10k_custom.html
[r10k_run]: ./r10k_run.html
[r10k_ref]: ./r10k_ref.html

[environ_dir]: {{puppet}}/environments_configuring.html

After you've set up and configured r10k, deploy environments by running r10k on the command line.

Before you begin, you should have created your [control repo][repo], declared your modules in [Puppetfiles][puppetfile], and [configured r10k][r10k_config].

## Initial r10k run

The first time you run r10k, deploy all environments and modules.

1. On the command line, run:

``` bash
r10k deploy environment
```

This command:

1. Checks your control repository to see what branches are present.
2. Maps those branches to the Puppet directory environments.
3. Clones your Git repo, and either creates (if this is your first run) or updates (if it is a subsequent run) your directory environments with the contents of your repo branches.

> Note that when running commands to deploy code on a master, r10k needs write access to your Puppet environment path. With Puppet Enterprise, run r10k as the `pe-puppet` user or as root. Running the user as root requires access control to the root user.

## Updating environments

### Update all environments

The `deploy environment` command updates all existing environments and recursively creates new environments. This command updates modules only on the first deployment of a given environment. On subsequent updates, it updates only the environment itself.

1. From the command line, run:

``` bash
r10k deploy environment
```

### Update all environments and modules

With the `--puppetfile` flag, the `deploy environment` command updates all environments and modules.

This command:

* Updates all sources.
* Creates new environments.
* Deletes old environments.
* Recursively updates all environment modules specified in environment Puppetfiles.

> **Note**: This command does the maximum possible r10k work, and is therefore the slowest method for r10k deployments. Usually, you should use the less resource-intensive commands for updating environments and modules.

1. From the command line, run:

``` bash
r10k deploy environment --puppetfile
```

### Update a single environment

To updated just one environment, specify that environment name with the `deploy environment` command. This command updates modules only on the first deployment of the specified environment. On subsequent updates, it updates only the environment itself.

If you're actively developing on a given environment, this is the quickest way to deploy your changes.

1. From the command line, run:

``` bash
r10k deploy environment <MY_WORKING_ENVIRONMENT>
```

### Update a single environment and its modules

To update both a single given environment and all of the modules contained in that environment's Puppetfile, add the `--puppetfile` flag to your command. This is useful if you want to make sure that a given environment is fully up to date.

1. From the command line, run:

``` bash
r10k deploy environment <MY_WORKING_ENVIRONMENT> --puppetfile
```

## Installing and updating modules

To update modules, use the `r10k deploy module` subcommand. This command installs or updates the modules you've specified in each environment's [Puppetfile][puppetfile]. If the specified module is not described in an environment's Puppetfile, that environment is skipped.

### Update certain module across all environments

To update specific modules across all environments, specify the modules with the `deploy module` command. You can specify a single module or more than one. This is useful when you're working on a module specified in a Puppetfile and want to update it across all environments.

1. To install the apache, jenkins, and java modules, run:

``` bash
r10k deploy module apache jenkins java
```

### Update one or more modules in a single environment

To update specific modules in a single environment, specify:

* The environment option `-e`.
* The name of the environment.
* The names of the modules you want to update in that environment.

1. To install the apache, jenkins, and java modules in the production environment, run:

``` bash
r10k deploy module -e production apache jenkins java
```

## View environments

Display information about your environments and modules with the `r10k deploy display` subcommand. This subcommand does not deploy environments, but only displays information about the environments and modules r10k is managing. You can view varying levels of detail about the environments.

* To display all environments being managed by r10k, use the `deploy display` command. On the command line, run:

  ``` bash
r10k deploy display
  ```

* To display all r10k-managed environments and Puppetfile modules, use the Puppetfile flag, `-p`. On the command line, run:

  ``` bash
r10k deploy display -p
  ```

* To get detailed information about module versions, use the `-p` (Puppetfile) and `--detail` flags. On the command line, run:

  ``` bash
r10k deploy display -p --detail
  ```
  
  This provides both the expected and actual versions of the modules listed in each environment's Puppetfile. 

* To get detailed information about the modules only in specific environments, limit your query with the environment names. To see details on the modules for environments called production, vmwr, and webrefactor, run:

  ``` bash
r10k deploy display -p --detail production vmwr webrefactor
  ```

  This provides both the expected and actual versions of the modules listed in the Puppetfile in each specified environment.

  



