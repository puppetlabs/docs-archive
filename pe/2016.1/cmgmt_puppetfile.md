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



Code Manager and r10k both manage modules through Puppetfiles. Puppetfiles specify detailed information about the modules from the Forge or other source code repositories that you want to install in each environment.

## About Puppetfiles

Puppetfiles are text files written in a Ruby-based DSL. They specify what modules you want to install in each environment, what version of those modules you want to install, and where you want to get the modules from. This allows Code Manager or r10k to install modules into a given environment in your deployment.

>**Note:** Puppetfiles do NOT include dependency resolution. You must make sure that you have every module needed for all of your specified modules to run. In addition, symlinks are unsupported; when you install modules with Code Manager or r10k, symlinks are not installed.

### Including your own modules

If you develop your own modules, maintain them in version control and include them in your Puppetfile as you would [declare any module](#declare-modules-in-puppetfiles). Code management purges any content that is in the control repo's module directory but is not listed in the Puppetfile. (The control repo modulesdir is, by default `./modules` relative to the location of the Puppetfile.)

## Creating and editing Puppetfiles

Each branch or environment in which you want to manage modules needs a Puppetfile. You'll start with a production Puppetfile, and then edit the Puppetfiles in future branches as needed.

1. Create a file named Puppetfile (with an uppercase "P" and no file extension) in your default branch or environment; this is usually the 'production' branch.
2. [Declare modules](#declare-modules-in-puppetfiles) in your production Puppetfile with the `mod` setting, as detailed below.
3. As you create branches based on the production branch, edit each branch's Puppetfile as needed.

### Declare modules in Puppetfiles

The `mod` setting specifies the modules that you want to install. Specify the module [long name]({{puppet}}/modules_publishing.html#a-note-on-module-names) in a string. You can specify the latest version, either with or without updating that version, or you can specify a particular version of a module to be maintained at that version.

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

### Change the module installation directory

By default, Code Manager and r10k install modules in a modules directory in the same directory the Puppetfile is in. For example, the following line installs the apache module into `./modules/apache`.

```
mod 'puppetlabs/apache'
```

If you are using r10k only for code management, you can point to another directory for module installation. Specify either an absolute or a relative path. This is supported only in r10k; **do not** change the module installation path if you are using Code Manager.

**Note:** Declare this setting at the top of the Puppetfile, before you list any modules.

#### To install modules to an absolute path:

```
moduledir '/a/development/environment/modules'

mod 'puppetlabs/apache'
#installs the apache module into '/a/development/environment/modules/apache
```

#### To install modules to a relative path:

```
moduledir 'thirdparty'

mod 'puppetlabs/apache'
# installs the apache module into './thirdparty/apache'
```

## Next step

After you've specified your modules in Puppetfiles, you're ready to configure [Code Manager][code_mgr] (recommended) or [r10k][r10k].

