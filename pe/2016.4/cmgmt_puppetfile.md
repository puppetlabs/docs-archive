---
layout: default
title: "Managing environment content with Puppetfiles"
canonical: "/pe/latest/cmgmt_puppetfile.html"
description: "A guide to managing modules and Hiera data with Puppetfiles for Puppet code management."
---

[repo]: ./cmgmt_control_repo.html
[puppetfile]: ./cmgmt_puppetfile.html
[code_mgr]: ./code_mgr.html
[r10k]: ./r10k.html
[modules]: {{puppet}}/modules_fundamentals.html
[forge]: forge.puppet.com
[custom]: ./code_mgr_custom.html
[r10k_custom]: ./r10k_custom.html
[private_key]: ./code_mgr_custom.html#private-key
[repo_setting]: ./code_mgr_custom.html#repositories
[code_mgr_config]: ./code_mgr_config.html
[r10k_config]: ./r10k_config.html



Code Manager and r10k both install and manage the content of your environments with Puppetfiles. Puppetfiles specify detailed information about each environment's Puppet code and data, including where to get that code and data from, where to install it, and whether to update it.

## About Puppetfiles

Puppetfiles are text files written in a Ruby-based DSL. They specify what content you want in each environment, what version of that content you want, and where you want to get the content from. Typically, this content includes:

* Modules from the Puppet Forge
* Modules from Git repositories
* Hiera data from Git repositories

>**Note:** Puppetfiles do NOT include dependency resolution. You must make sure that you have every module needed for all of your specified modules to run. In addition, symlinks are unsupported; when you install modules with Code Manager or r10k, symlinks are not installed.

## About modules

Modules are collections of Puppet code and data with a specific directory structure. Almost all Puppet manifests are kept in modules. To learn more about module basics, see our [module fundamentals][modules] page.

With Code Manager and r10k, **do not** use the `puppet module` command to install or manage modules. Instead, you'll use Puppetfiles in each of your environments to install, update, and manage your modules. Be aware that Code Manager purges any modules you've installed to the live code directory with `puppet module install`. 

### Including your own modules

If you develop your own modules, maintain them in version control and include them in your Puppetfile as you would [declare any module](#declare-content-in-a-puppetfile). Code management purges any content that is in the control repo's module directory but is not listed in the Puppetfile. (The control repo modulesdir is, by default, `./modules` relative to the location of the Puppetfile.)

## Create and edit a Puppetfile

Each branch or environment in which you want to manage content needs a Puppetfile. You'll start with a production Puppetfile, and then edit the Puppetfiles in future branches as needed.

1. Create a file named Puppetfile (with an uppercase "P" and no file extension) in your default branch or environment; this is usually the 'production' branch.
2. [Declare environment content](#declare-content-in-a-puppetfile) in your production Puppetfile with the `mod` directive, as detailed below.
3. As you create branches based on the production branch, edit each branch's Puppetfile as needed.

## Declare content in a Puppetfile

Use the `mod` directive to specify the content that you want to install. You can declare modules [from the Puppet Forge](#declare-a-module-from-the-puppet-forge), as well as either module or [non-module content](#declare-non-module-content) (such as Hiera data) [from a Git repository](#declare-content-from-a-git-repo).


### Declare a module from the Puppet Forge

Specify the module [long name]({{puppet}}/modules_publishing.html#a-note-on-module-names) in a string. You can specify the latest version, either with or without updating that version, or you can specify a particular version of a module to be maintained at that version. By default, Code Manager and r10k install modules in a modules directory (`./modules`) in the same directory the Puppetfile is in. If needed, you can [change the module installation path](#optional-change-the-module-installation-directory).

To install modules from the Puppet Forge, and then keep the module at the installed version, declare the module without additional options:

``` ruby
mod 'puppetlabs/apache'
mod 'adrienthebo/thebestmoduleever'
```

To install a specific version of a module, and then keep the module at that version, add the version number:

``` ruby
mod 'puppetlabs/apache', '0.10.0'
```

To install the latest module version, and then keep the module updated, use `:latest`:

``` ruby
mod 'puppetlabs/apache', :latest
```

#### Optional: Change the module installation directory

By default, Code Manager and r10k install modules in the "modules" directory within the same directory as the Puppetfile. For example, the following line installs the apache module into `./modules/apache`.

``` ruby
mod 'puppetlabs/apache'
```

However, you can specify a different default module installation path with the `moduledir` directive. Set `moduledir` as a relative path at the top of the Puppetfile, **before** you list any modules.

``` ruby
moduledir 'thirdparty'

mod 'puppetlabs/apache'
# installs the apache module into './thirdparty/apache'
```

The `moduledir` directive applies to all content declared in the Puppetfile. To set installation paths for only some modules or content, declare those content sources [as Git repositories](#declare-content-from-a-git-repo) and use the `install_path` option. The `install_path` option overrides the `moduledir` directive.

If you are using **r10k only**, you can specify an absolute path instead. This is supported only in r10k; **do not** use absolute paths with Code Manager.

``` ruby
moduledir '/a/development/environment/modules'

mod 'puppetlabs/apache'
#installs the apache module into '/a/development/environment/modules/apache
```

### Declare non-module content

In addition to modules, you can also manage non-module content, such as data, in your environments. The most common case for this is when you want to manage Hiera data that is stored in a repository other than your control repo. To do this, [declare the Git repository](#declare-content-from-a-git-repo) content as described below and specify the installation directory in the environment with the `install_path` option. 

Note that if you do not specify `install_path` for non-module content, it is installed in the "modules" directory and treated as a module.

### Declare content from a Git repo

Specify module or data content from a Git repository with the `:git` option. This installs the content, and then keeps it updated with the master branch of that Git repo.

For example, to install the Puppet apache module, and then keep it updated to the master branch of the puppetlabs/puppetlabs-apache repo:

``` ruby
mod 'apache',
  :git => 'https://github.com/puppetlabs/puppetlabs-apache'
```

Additional options allow you to:

* [Access a repository using an SSH private key.](#use-ssh-private-key-authentication)
* [Specify a particular version of the repository.](#specify-the-repository-version)
* [Track a branch within the control repo.](#track-a-branch-within-the-control-repo)
* [Specify an installation path for the repo's content.](#specify-install-paths-for-repositories)

#### Use SSH private key authentication

To use SSH private key authentication, declare your module using the SSH URL:

``` ruby
mod 'myco/privatemod',
 :git => 'git@git.example.com:myco/privatemod.git'
```

Then, configure the correct private key by setting [Code Manager][custom] or [r10k][r10k_custom] parameters in Hiera:

  * To set a key for all Git operations, use the `private-key` setting under [`git-settings`][private_key].
  * To set a private key for an individual remote, set the private key in the [`repositories`][repo_setting] hash in `git-settings` for each specific remote.

#### Specify the repository version

To specify a particular version of the repository, use the `ref`, `tag`, `commit`, or `branch` options.

* `ref`: Specifies the Git reference to check out. This option can reference either a tag, a commit, or a branch.
* `tag`: Specifies the repo by a certain tag value.
* `commit`: Specifies the repo by a certain commit.
* `branch`: Specifies a specific branch of the repo.

For example, to install puppetlabs/apache and pin it to the '0.9.0' tag:

``` ruby
mod 'apache',
  :git => 'https://github.com/puppetlabs/puppetlabs-apache',
  :tag => '0.9.0'
```

To install puppetlabs/apache and pin it to the '8df51aa' commit:

``` ruby
mod 'apache',
  :git    => 'https://github.com/puppetlabs/puppetlabs-apache',
  :commit => '8df51aa'
```

To install puppetlabs/apache and track the 'proxy_match' branch:

``` ruby
mod 'apache',
  :git    => 'https://github.com/puppetlabs/puppetlabs-apache',
  :branch => 'proxy_match'
```

#### Track a branch within the control repo

To track a branch within the control repo, set the `:branch` option to the special value `:control_branch`. This tracks a branch matching the control repo branch in which the Puppetfile is located. For example, for a Puppetfile in the "testing" branch of a control repo, setting `:control_branch` deploys content from the "testing" branch of the content repo.

``` ruby
# Deploy content from branch matching control repo branch.
mod 'hieradata',
  :git    => 'git@git.example.com:organization/hieradata.git',
  :branch => :control_branch
```

When you create new branches from this branch of your control repo, this relative branch tracking means that you don't have to edit the new Puppetfile as extensively.

#### Set a default branch for content deployment

You can set a `:default_branch` option, which specifies what branch to deploy content from if the given `:ref`, `:tag`, `:commit`, or `:branch` option cannot be resolved and deployed. This is mostly useful when you set `:branch` to the `control_branch` value. If the desired content cannot be resolved and no default branch is given, or if the default branch cannot be resolved, an error is logged and the content is not deployed or updated. 
 
``` ruby
# Track control branch and fall-back to master if no matching branch.
mod 'hieradata',
  :git => 'git@git.example.com:organization/hieradata.git',
  :branch => :control_branch,
  :default_branch => 'master'
```

#### Specify install paths for repositories

When you declare Git repo content in your Puppetfile, you can specify installation paths for each repository you declare. This allows you to separate non-module content in your directory structure. You can also set specific installation paths for individual modules from Git repositories.

Specify the installation path in the environment with the `install_path` option. For example, to install site data content from a Git repository into the `./hieradata/site_data` subdirectory of the environment, declare it in the Puppetfile:

``` ruby
mod 'site_data', 
  :git => 'git@git.example.com:site_data.git',
  :install_path => 'hieradata'
```

To install the content into a directory at the root of the environment, specify an empty value to the `install_path` option:

```
mod 'site_data',
  :git => 'git@git.example.com:site_data.git',
  :install_path =>  ''
```

This installs the content into the `./site_data` subdirectory.

If you do not set `install_path`, content is installed as modules in the environment's default `./modules` directory or in the [`moduledir`](#optional-change-the-module-installation-directory), if you set this in the Puppetfile.

## Next step

After you've specified your modules in Puppetfiles, you're ready to configure code management.

Configuration steps differ, depending on whether you're using Code Manager (recommended) or r10k. For important information about the function and limitations of these tools, see the [Code Manager][code_mgr] page or the [r10k][r10k] page. For configuration instructions, see [Code Manager configuration][code_mgr_config] or [r10k configuration][r10k_config].


