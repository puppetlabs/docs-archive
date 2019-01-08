---
layout: default
title: "Deploying Environments with r10k"
canonical: "/pe/latest/r10k_run.html"
description: "Deploying environments and modules with r10k, a Puppet code management tool."
---

[environ_dir]: /puppet/latest/reference/environments_configuring.html
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

[environ_dir]: /puppet/latest/reference/environments_configuring.html

After you've created your [control repo][repo], declared your modules in [Puppetfiles][puppetfile], and [configured r10k][r10k_config], it's time to run r10k.

## Initial r10k run

The first time you run r10k, deploy all environments and modules with the following command:

~~~
r10k deploy environment
~~~

This command:

1. Checks your control repository to see what branches are present.
2. Maps those branches to the Puppet directory environments.
3. Clones your Git repo, and either creates (if this is your first run) or updates (if it is a subsequent run) your directory environments with the contents of your repo branches.

> Note that when running commands to deploy code on a master, r10k needs write access to your Puppet environment path. If you're using Puppet Enterprise this account is pe-puppet. To do this, run r10k as the Puppet user or as root. Running the user as root requires access control to the root user.

## Updating Environments

### Update all environments

~~~
r10k deploy environment
~~~

This command updates existing environments and recursively creates new environments.

When an environment is deployed for the first time (as in the initial r10k run above), this command automatically updates all modules specified in each environment's Puppetfile as well. For subsequent updates, only the environment itself is updated.

### Update all environments and modules

To update all environments and modules on subsequent r10k runs, run the same command, with the `--puppetfile` flag:

~~~
r10k deploy environment --puppetfile
~~~

This command:

* Updates all sources.
* Creates new environments.
* Deletes old environments.
* Recursively updates all environment modules specified in environment Puppetfiles.

However, you should note that this is the slowest method for using r10k because it does the maximum possible work. Use less resource-intensive commands for most updates of your environments and modules.

### Update a single environment

~~~
r10k deploy environment <MY_WORKING_ENVIRONMENT>
~~~

When you're actively developing on a given environment, this is the best way to deploy your changes. When an environment is deployed for the first time, it automatically updates all modules specified by the environment's Puppetfile as well. For subsequent updates, only the environment itself is updated.

### Update a single environment and its modules

~~~
r10k deploy environment <MY_WORKING_ENVIRONMENT> --puppetfile
~~~

This updates the given environment and all modules contained in its Puppetfile. This is useful if you want to make sure that a given environment is fully up to date.

## Installing and Updating modules

When you use the `r10k deploy module` subcommand to update modules, r10k installs or updates the modules as you've specified them in each environment's [Puppetfile][puppetfile]. If the specified module is not described in an environment's Puppetfile, that environment will be skipped.

### Update a single module across all environments

~~~
r10k deploy module apache
~~~

This is useful for when you're working on a module specified in a Puppetfile and want to update it across all environments.

### Update multiple modules across all environments

~~~
r10k deploy module apache jenkins java
~~~

### Update one or more modules in a single environment

~~~
r10k deploy module -e production apache jenkins java
~~~

## Viewing environments

You can display information about your environments and modules with the `display` subcommand.

### Display all environments being managed by r10k

~~~
r10k deploy display
~~~

### Display environments and modules

To display all r10k-managed environments and Puppetfile modules:

~~~
r10k deploy display -p
~~~

You can get detailed information about the expected and actual module versions by adding the `--detail` flag to this command:

~~~
r10k deploy display -p --detail
~~~

### Display specific environments and modules

For an list of specific environments being managed by r10k, and the modules specified in their Puppetfiles along with their expected and actual versions, limit your query with the environment names. For example:

~~~
r10k deploy display -p --detail production vmwr webrefactor
~~~

The above example displays details on the modules for environments called production, vmwr, and webrefactor.

