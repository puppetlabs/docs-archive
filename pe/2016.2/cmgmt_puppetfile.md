---
layout: default
title: "Managing modules with Puppetfiles"
canonical: "/pe/latest/cmgmt_puppetfile.html"
description: "A guide to managing modules with Puppetfiles for Puppet code management."
---

[repo]: ./cmgmt_control_repo.html
[puppetfile]: ./cmgmt_puppetfile.html
[code_mgr]: ./code_mgr.html
[r10k]: ./r10k.html
[modules]: {{puppet}}/modules_fundamentals.html
[forge]: forge.puppet.com
[custom]: ./code_mgr_custom.html
[private_key]: ./code_mgr_custom.html#private-key
[repo_setting]: ./code_mgr_custom.html#repositories

Code Manager and r10k both install and manage modules with Puppetfiles. Puppetfiles specify detailed information about the modules that you want to install in each environment, whether the modules are from the Forge or from other source code repositories.

## About Puppetfiles

Puppetfiles are text files written in a Ruby-based DSL. They specify what modules you want to install in each environment, what version of those modules you want to install, and where you want to get the modules from. This allows Code Manager or r10k to install modules into a given environment in your deployment.

>**Note:** Puppetfiles do NOT include dependency resolution. You must make sure that you have every module needed for all of your specified modules to run. In addition, symlinks are unsupported; when you install modules with Code Manager or r10k, symlinks are not installed.

## About modules

Modules are collections of Puppet code and data with a specific directory structure. Almost all Puppet manifests are kept in modules. To learn more about module basics, see our [module fundamentals][modules] page.

With Code Manager and r10k, do not use the `puppet module` command to install or manage modules. Instead, you'll use Puppetfiles in each of your environments to install, update, and manage your modules. Be aware that Code Manager purges any modules you've installed to the live code directory with `puppet module install`. 

### Including your own modules

If you develop your own modules, maintain them in version control and include them in your Puppetfile as you would [declare any module](#declare-modules-in-puppetfiles). Code management purges any content that is in the control repo's module directory but is not listed in the Puppetfile. (The control repo modulesdir is, by default, `./modules` relative to the location of the Puppetfile.)

## Creating and editing Puppetfiles

Each branch or environment in which you want to manage modules needs a Puppetfile. You'll start with a production Puppetfile, and then edit the Puppetfiles in future branches as needed.

1. Create a file named Puppetfile (with an uppercase "P" and no file extension) in your default branch or environment; this is usually the 'production' branch.
2. [Declare modules](#declare-modules-in-puppetfiles) in your production Puppetfile with the `mod` setting, as detailed below.
3. As you create branches based on the production branch, edit each branch's Puppetfile as needed.

### Declare modules in Puppetfiles

The `mod` setting specifies the modules that you want to install. Specify the module [long name]({{puppet}}/modules_publishing.html#a-note-on-module-names) in a string. You can specify the latest version, either with or without updating that version, or you can specify a particular version of a module to be maintained at that version.

Code Manager and r10k install modules in a modules directory (`./modules`) in the same directory the Puppetfile is in.

**Install the latest version of the module, and then keep the module at that version:**

```
mod 'puppetlabs/apache'
mod 'adrienthebo/thebestmoduleever'
```

**Install a specific version, and then keep the module at that version:**

```
mod 'puppetlabs/apache', '0.10.0'
```

**Install the latest available version of a module, and then continue to update the module:**

```
mod 'puppetlabs/apache', :latest
```

#### Declare a Git repo as a module

You can also specify a Git repo that contains a Puppet module. r10k then copies that repo and uses it as a module. In this case, you can specify the module "version" by using the `ref`, `tag`, `commit`, and `branch` options.

* `ref`: Determines the Git reference to check out. Can be any of a tag, commit, or branch.
* `tag`: Clones the repo at a certain tag value.
* `commit`: Clones the repo at a certain commit.
* `branch`: Clones a specific branch of the repo.

**Install puppetlabs/apache and keep it up to date with 'master':**

```
mod 'apache',
  :git => 'https://github.com/puppetlabs/puppetlabs-apache'
```

**Install puppetlabs/apache and track the 'docs_experiment' branch:**

```
mod 'apache',
  :git => 'https://github.com/puppetlabs/puppetlabs-apache',
  :ref => 'docs_experiment'
```

**Install puppetlabs/apache and pin to the '0.9.0' tag:**

```
mod 'apache',
  :git => 'https://github.com/puppetlabs/puppetlabs-apache',
  :tag => '0.9.0'
```

**Install puppetlabs/apache and pin to the '8df51aa' commit:**

```
mod 'apache',
  :git    => 'https://github.com/puppetlabs/puppetlabs-apache',
  :commit => '8df51aa'
```

**Install puppetlabs/apache and track the 'proxy_match' branch:**

```
mod 'apache',
  :git    => 'https://github.com/puppetlabs/puppetlabs-apache',
  :branch => 'proxy_match'
```

**Use SSH private key authentication for a Git repo**

To use SSH private key authentication, declare your module using the SSH URL:

```
mod 'myco/privatemod',
 :git => 'git@git.example.com:myco/privatemod.git'
```

Then, configure the correct private key by setting [Code Manager parameters][custom] in Hiera:

  * To set a key for all Git operations, use the `private-key` setting under [`git-settings`][private_key].
  * To set a private key for an individual remote, set the private key in the [`repositories`][repo_setting] hash in `git-settings` for each specific remote.


### Optional: Change the module installation directory

By default, Code Manager and r10k install modules in a modules directory within the same directory as the Puppetfile. For example, the following line installs the apache module into `./modules/apache`.

```
mod 'puppetlabs/apache'
```

However, you can specify another module installation path if needed. Declare this setting at the top of Puppetfile, **before** you list any modules. You can specify a relative path for Code Manager or r10k.

```
moduledir 'thirdparty'

mod 'puppetlabs/apache'
# installs the apache module into './thirdparty/apache'
```

If you are using **r10k only**, you can also specify an absolute path. This is supported only in r10k; **do not** use absolute paths with Code Manager.


#### To install modules to an absolute path:

```
moduledir '/a/development/environment/modules'

mod 'puppetlabs/apache'
#installs the apache module into '/a/development/environment/modules/apache
```


## Next step

After you've specified your modules in Puppetfiles, you're ready to configure [Code Manager][code_mgr] (recommended) or [r10k][r10k].

