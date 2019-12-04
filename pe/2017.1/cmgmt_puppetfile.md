---
layout: default
title: "Managing environment content with Puppetfiles"
canonical: "/pe/latest/cmgmt_puppetfile.html"
description: "Managing modules and Hiera data with Puppetfiles for Puppet code management."
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

Puppetfiles are text files that specify what content you want in each environment, what version of that content you want, and where you want to get the content from. Typically, this content includes:

* Modules from the Puppet Forge
* Modules from Git repositories
* Data from Git repositories

For each environment that you want to manage content in, you need a Puppetfile. You'll create a base Puppetfile in your default environment (usually `production`). As you create new branches based on your default branch, each environment inherits this base Puppetfile. You can then edit each environment's Puppetfile as needed.

>**Note:** Puppetfiles do NOT include dependency resolution. You must make sure that you have every module needed for all of your specified modules to run. In addition, symlinks are unsupported; when you install modules with Code Manager or r10k, symlinks are not installed.

## About modules

Modules are collections of Puppet code and data with a specific directory structure. Almost all Puppet manifests are kept in modules. To learn more about module basics, see our [module fundamentals][modules] page.

By default, Code Manager and r10k install content in a modules directory (`./modules`) in the same directory the Puppetfile is in. For example, declaring the apache module in the Puppetfile normally installs the module into `./modules/apache`.

With Code Manager and r10k, **do not** use the `puppet module` command to install or manage modules. Instead, you'll use Puppetfiles in each of your environments to install, update, and manage your modules. Be aware that Code Manager purges any modules you've installed to the live code directory with `puppet module install`. 

### Including your own modules

If you develop your own modules, maintain them in version control and include them in your Puppetfile as you would [declare any module from a repo](#declare-modules-or-data-content-from-git-repositories). If you have content in your control repo's module directory that is *not* listed in your Puppetfile, code management purges it. (The control repo module directory is, by default, `./modules` relative to the location of the Puppetfile.)

## Create a Puppetfile

Create a Puppetfile in your production branch, and then edit it to declare the environment content in your production Puppetfile with the `mod` directive.

In the Puppetfile, you can declare:

  * Modules from the Puppet Forge.
  * Modules from a Git repository.
  * Data or other non-module content (such as Hiera data) from a Git repository.

You can declare any or all of this content as needed for each environment. As you create branches based on your production branch, edit each branch's Puppetfile as needed.

Before you begin, you should already have [a control repo][repo] with a production branch. These instructions assume that production is the default branch of your control repo.

1. On your production branch, from the command line, create a file named `Puppetfile` (with an uppercase "P" and no file extension):
   
   `touch Puppetfile`

2. Open the Puppetfile in your text editor to declare modules and data content for your environment.

## Optional: Change the default module installation directory

To specify a module installation path other than the default modules directory (`./modules`), use the `moduledir` directive. This directive applies to all content declared in the Puppetfile. You must specify this as a relative path at the top of the Puppetfile, **before** you list any modules.

1. Set `moduledir` to your module installation directory:

   ``` ruby
moduledir 'thirdparty'
   ```

To set installation paths for only certain modules or data, declare those content sources [as Git repositories](#declare-content-from-a-git-repo) with the `install_path` option. The `install_path` option overrides the `moduledir` directive.

## Declare modules from the Puppet Forge

List the modules you want to install with `mod`, the module [long name]({{puppet}}/modules_publishing.html#a-note-on-module-names) in a string, and what version of the module you want to track. You can specify the most recent version of a module, with or without updates, or you can specify a specific version of a module.

* To install the latest version of a Forge module and keep the module at that version, declare the module without additional options:

  ``` ruby
mod 'puppetlabs/apache'
mod 'adrienthebo/thebestmoduleever'
  ```
  
* To install the latest module version, and then keep the module updated, use `:latest`:

  ``` ruby
mod 'puppetlabs/apache', :latest
  ```

* To install a specific version of a module, and then keep the module at that version, add the version number:

  ``` ruby
mod 'puppetlabs/apache', '0.10.0'
  ```
  
## Declare modules or data content from Git repositories

List the modules, data, or other non-module content that you want to install from a Git repository. When you declare content Specify repo content with the `:git` option.

* To install a module and keep it updated to the master branch, declare the module name and specify the `:git` repository:

  ``` ruby
mod 'apache',
  :git => 'https://github.com/puppetlabs/puppetlabs-apache'
  ```

* To install data or other non-module content in your environments, declare the content and specify both the repository and the installation directory: 
  
  ``` ruby
mod 'site_data', 
  :git: 'git@git.example.com:site_data.git',
  :install_path: 'hieradata'
  ```

> **Note**: If you do not specify `install_path` for content, it is installed in the modules directory and treated as a module. Use the `install_path` to separate your data from modules.

### Specify installation paths for repositories

You can specify individual installation paths for repository content you declare in the Puppetfile. This allows you to separate non-module content in your directory structure or to set specific installation paths for individual modules. The `install_path` option overrides the [`moduledir`](#optional-change-the-module-installation-directory). 

* To install the content into a subdirectory in the environment, specify the directory with the `install_path` option:

   ``` ruby
mod 'site_data', 
  :git => 'git@git.example.com:site_data.git',
  :install_path => 'hieradata'
   ```
   
   This example installs site data content from a Git repository into the environment's `./hieradata/site_data` subdirectory.

* To install the content into a directory at the root of the environment, specify an empty value to the `install_path` option:

   ```
mod 'site_data',
  :git => 'git@git.example.com:site_data.git',
  :install_path => ''
   ```

   This example installs the site data content into the `./site_data` subdirectory.

### Declare module or data content with SSH private key authentication

   1. Declare your repository content, specifying the Git repo by the SSH URL:

      ``` ruby
mod 'myco/privatemod',
 :git => 'git@git.example.com:myco/privatemod.git'
      ```

   2. Configure the correct private key by setting [Code Manager][custom] or [r10k][r10k_custom] parameters in Hiera:

      * To set a key for all Git operations, use the `private-key` setting under [`git-settings`][private_key].
      * To set a private key for an individual remote, set the private key in the [`repositories`][repo_setting] hash in `git-settings` for each specific remote.


### Keep repository content at a specific version

To specify a particular repository version, declare the version you want to track with the following options:

* `ref`: Specifies the Git reference to check out. This option can reference either a tag, a commit, or a branch.
* `tag`: Specifies the repo by a certain tag value.
* `commit`: Specifies the repo by a certain commit.
* `branch`: Specifies a specific branch of the repo.
* `default_branch`: Specifies a default branch to use for deployment if the specified `ref`, `tag`, `commit`, or `branch` cannot be deployed. Must be used with one of the other version options. Useful if you are tracking a [relative branch](#declare-content-from-a-relative-control-repo-branch) of the control repo.

Setting these options maintains the repository at that version and deploys any updates made to that particular version. Declare the content, specifying the repository and version you want to track: 

* To install `puppetlabs/apache` and pin it to the '0.9.0' tag:

  ``` ruby
mod 'apache',
  :git => 'https://github.com/puppetlabs/puppetlabs-apache',
  :tag => '0.9.0'
  ```

* To install `puppetlabs/apache` and pin it to the '8df51aa' commit:

  ``` ruby
mod 'apache',
  :git    => 'https://github.com/puppetlabs/puppetlabs-apache',
  :commit => '8df51aa'
  ```

* To install puppetlabs/apache and track the 'proxy_match' branch:

  ``` ruby
mod 'apache',
  :git    => 'https://github.com/puppetlabs/puppetlabs-apache',
  :branch => 'proxy_match'
  ```

### Declare content from a relative control repo branch

The `branch` option also has a special `:control_branch` option, which allows you to deploy content from a control repo branch relative to the location of the Puppetfile. 

Normally, `branch` tracks a specific named branch of a repo, such as testing. If you set it to `:control_branch`, it instead tracks whatever control repo branch the Puppetfile is in. For example, if your Puppetfile is in the production branch, content from the production branch is deployed; if a duplicate Puppetfile is located in testing, content from testing is deployed. This means that as you create new branches, you don't have to edit the inherited Puppetfiles as extensively.

1. To track a branch within the control repo, declare the content with the `:branch` option set to `:control_branch`:

   ``` ruby
# Deploy content from branch matching control repo branch.
mod 'hieradata',
  :git    => 'git@git.example.com:organization/hieradata.git',
  :branch => :control_branch
   ```

### Set a default branch for content deployment

You can set a `default_branch` option, which specifies what branch to deploy content from if the given `ref`, `tag`, `commit`, or `branch` option cannot be resolved and deployed. This is mostly useful when you set `branch` to the `:control_branch` value.

If the desired content cannot be resolved and no default branch is given, or if the default branch cannot be resolved, an error is logged and the content is not deployed or updated. 
 
1. In the Puppetfile, in the content declaration, set the `default_branch` option to the branch you want to fall back to.

   ``` ruby
# Track control branch and fall-back to master if no matching branch.
mod 'hieradata',
  :git    => 'git@git.example.com:organization/hieradata.git',
  :branch => :control_branch,
  :default_branch => 'master'
   ```

## Next step

After you've specified your modules in Puppetfiles, you're ready to configure code management. Configuration steps differ, depending on whether you're using Code Manager (recommended) or r10k. For important information about the function and limitations of these tools, see the [Code Manager][code_mgr] page or the [r10k][r10k] page. For configuration instructions, see [Code Manager configuration][code_mgr_config] or [r10k configuration][r10k_config].


