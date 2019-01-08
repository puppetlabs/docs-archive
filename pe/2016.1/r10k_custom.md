---
layout: default
title: "Customizing r10k configuration"
canonical: "/pe/latest/r10k_custom.html"
description: "Configuring r10k code management in the Puppet Enterprise console."
---

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
[run]: ./r10k_run.html
[r10kindex]: ./r10k.md
[add_class]: ./console_classes_groups.html#adding-classes-to-a-node-group
[hiera]: ./config_intro.html#configure-settings-with-hiera

  
If you need a more highly customized r10k configuration, customize r10k's configuration by setting parameters for the `pe_r10k` class [in Hiera][hiera].
 
To set the `pe_r10k` class parameters by using Hiera, add keys to your control repository hierarchy in the `hieradata/common.yaml` file. Set `pe_r10k::<parameter>` for the specific parameter. For example:

```
pe_r10k::cachedir: /var/cache/r10k
pe_r10k::remote: git://git-server.site/my-org/main-modules
```

> Warning: Do not edit r10k’s configuration file manually. Puppet manages this configuration automatically and will undo any manual changes you make.

## Parameters

### `cachedir`

Specifies the path to the directory where you want the cache of your Git repos to be stored.

Accepts a string, such as `/var/cache/r10k`.

### `git_settings`

A hash containing Git-specific settings, such as:

```yaml
provider: "rugged"
  private_key: "/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa"
  username: "git"
```
You can specify the following in `git_settings`:

#### `provider`

Specifies what Git provider to use. You can use one of two providers for r10k: rugged and shellgit.

We recommend the Rugged provider, which is also the only supported provider for r10k answer file or master profile configuration. Rugged requires Ruby 1.9 or greater.

However, existing r10k installations and customized configurations default to the shellgit provider. If you are using shellgit, [install Git](http://git-scm.com/book/en/v2/Getting-Started-Installing-Git) on both your Puppet master and on the Puppet code development machine.

#### `private_key`

The path to your private SSH key, such as `'/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa'`.

#### `repositories`

A list of repositories and their respective private keys. Use this setting if you want to use multiple control repos. To use multiple control repos, the `sources` setting and the `repositories` setting must match.

  * `remote`: The repository these settings should apply to.
  * `private_key`: The file containing the private key to use for this repository. This file must have read permissions for the `pe-puppet` user. This setting overrides the global `private_key` setting.
  
This setting accepts a hash in the following format:
  
```yaml
git:
  repositories:
    - remote: "ssh://tessier-ashpool.freeside/protected-repo.git"
      private_key: "/etc/puppetlabs/r10k/ssh/id_rsa-protected-repo-deploy-key"
```

#### `username`

Optionally, specify a username when the Git remote URL does not provide a username. 

### `postrun`

An optional command to be run after r10k finishes deploying environments. The command must be an array of strings to be used as an argument vector.

```yaml
  - "/usr/bin/curl"
  - "-F"
  - "deploy=done"
  - "http://my-app.site/endpoint"
```

### `remote`

Specifies where the source repository should be fetched from. The remote must be able to be fetched without any interactive input. That is, you cannot be prompted for usernames or passwords in order to fetch the remote. If `remote` is set, you cannot use `sources`.

Accepts a string that is any valid SSH URL that r10k can clone, such as: 

`git://git-server.site/my-org/main-modules`

### `r10k_basedir`

Specifies the path where environments will be created for this source. This directory is entirely managed by r10k, and any contents that r10k did not put there will be removed. If `r10k_basedir` is not set, it uses the default `environmentpath` in your `puppet.conf` file. If `r10k_basedir` is set, you cannot set the `sources` parameter.

Note that the `r10k_basedir` setting **must match** the `environmentpath` in your puppet.conf file, or Puppet won't be able to access your new directory environments.

Accepts a string, such as: `/etc/puppetlabs/code/environments`.

### `sources` 

This setting is used if you are managing more than just Puppet environments, such as when you are also managing Hiera data in its own control repository. Note that if `sources` is set, you cannot use the `remote` and `r10k_basedir` settings above.

The `sources` setting can include the following subsettings:

* `remote`
* `basedir`
* `prefix`
* `invalid_branches`

This setting accepts a hash such as:

```yaml
myorg: 
  remote: "git://git-server.site/myorg/main-modules"
  basedir: "/etc/puppetlabs/puppet/environments"
  prefix: true
mysource: 
  remote: "git://git-server.site/mysource/main-modules"
  basedir: "/etc/puppetlabs/puppet/environments"
  prefix: "testing"
```

Note that r10k raises an error if different sources collide in a single base directory. If you are using multiple sources, use the [`prefix`](#prefix) option to prevent collisions.

#### `remote`

Specifies where the source repository should be fetched from. The remote must be able to be fetched without any interactive input. That is, you cannot be prompted for usernames or passwords in order to fetch the remote.

Accepts a string that is any valid URL that r10k can clone, such as: 

`git://git-server.site/my-org/main-modules`

#### `basedir`

Specifies the path where environments will be created for this source. This directory is entirely managed by r10k, and any contents that r10k did not put there will be removed.

Note that the `basedir` setting **must match** the `environmentpath` in your puppet.conf file, or Puppet won't be able to access your new directory environments.

#### `prefix`

Allows environment names to be prefixed with this string. Alternatively, if `prefix` is set to true, the source's name will be used. This prevents collisions when multiple sources are deployed into the same directory.

In the `sources` example above, two "main-modules" environments are set up in the same base directory. The prefix setting is used to differentiate the environments: the first will be named "myorg-main-modules", and the second will be "testing-main-modules".

#### `invalid_branches`

Specifies how branch names that cannot be cleanly mapped to Puppet environments are handled. This option accepts three values:

* 'correct_and_warn' is the default value and replaces non-word characters with underscores and issues a warning.
* 'correct' replaces non-word characters with underscores without warning.
* 'error' ignores branches with non-word characters and issues an error.

```yaml
mysource: 
  basedir: "/etc/puppetlabs/puppet/environments"
  invalid_branches: "error"
  remote: "git://git-server.site/mysource/main-modules"
```

## Next steps

Next, [run r10k][run] to deploy your environments. PE does not automatically run r10k at the end of installation.
