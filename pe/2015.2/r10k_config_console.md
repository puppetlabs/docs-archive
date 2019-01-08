---
layout: default
title: "PE 2015.2 » Code Management with r10k » Configuring r10k in the PE Console"
subtitle: "Configuring r10k in the PE Console"
canonical: "/pe/latest/r10k_config_console.html"
description: "Configuring r10k code management in the Puppet Enterprise console."
---

[environ_dir]: /puppet/latest/reference/environments_configuring.html
[install_prep]: ./r10k_install_prep.html
[config_answers]: ./r10k_config_answers.html
[puppetfile]: ./r10k_puppetfile.html
[run]: ./r10k_run.html
[r10kindex]: ./r10k.md
[puppetfile]: ./r10k_puppetfile.html
[add_class]: /pe/latest/console_classes_groups.html#adding-classes-to-a-node-group

You'll configure r10k in the PE console if:

* You are upgrading from an existing PE installation.
* You need a more complex setup than the answer file supports.
* You did not provide the remote and the private key in an answer file during PE installation.

### Switching from Profile to the PE Console

Note that you **cannot** configure r10k in the PE console if you added the remote and private key via the answer file or the master profile. To switch to configuring r10k in the console, you must first remove the `remote` and `private_key` keys from the master profile. Once you have done this, you can proceed.

## Configure r10k in the PE Console

1. In the PE console, navigate to the Classification page.
2. Click the PE Master group.
3. In the PE Master group page, click the Classes tab.
4. Locate or [add][add_class] the `pe_r10k` class.
5. In the `pe_r10k` class, set r10k's parameters with the keys and values [listed below](#parameters) as needed. At minimum, set the `remote` and `git_settings` parameters.
6. Click Add parameter, and then the Commit change button.

## Parameters

Note that the `sources` and `git_settings` parameters require JSON. If you are upgrading from a previous version of r10k, you can get help translating your existing `r10k.yaml` settings into JSON at [yamltojson.com](http://yamltojson.com).

### `cachedir`

Specifies the path to the directory where you want the cache of your Git repo(s) to be stored.

Accepts a string, such as `/var/cache/r10k`.

### `git_settings`

A hash containing Git-specific settings, listed below. The hash you provide this setting in the node classifier must be in JSON format, such as:

~~~json
{
    "provider": "rugged",
    "private_key": "/root/.ssh/id_rsa",
    "username": "git"
}
~~~

#### `provider`

Specifies what Git provider to use. You can use one of two providers for r10k: rugged and shellgit.

We recommend the rugged provider, which is also the only supported provider for r10k answer file configuration. However, existing r10k installations and customized configurations default to the shellgit provider. The rugged provider also requires Ruby 1.9 or greater.

#### `private_key`

The path to your private SSH key. If you specify the rugged provider, you must provide a private key. 

#### `username`

Optionally, specify a username when the Git remote URL does not provide a username. 

### `postrun`

An optional command to be run after r10k finishes deploying environments. The command must be an array of strings to be used as an argument vector. This array must be in JSON format, such as:

~~~json
[
  "/usr/bin/curl",
  "-F",
  "deploy=done",
  "http://my-app.site/endpoint"
]
~~~

### `remote`

Specifies where the source repository should be fetched from. The remote must be able to be fetched without any interactive input; e.g., you cannot be prompted for usernames or passwords in order to fetch the remote. If `remote` is set, you cannot use `sources`.

Accepts a string that is any valid URL that r10k can clone, such as: 

`git://git-server.site/my-org/main-modules`

### `r10k_basedir`

Specifies the path where environments will be created for this source. This directory is entirely managed by r10k, and any contents that r10k did not put there will be removed. If `r10k_basedir` is not set, it uses the default `environmentpath` in your `puppet.conf` file. If `r10k_basedir` is set, you cannot set the `sources` parameter.

Note that the `r10k_basedir` setting **must match** the `environmentpath` in your puppet.conf file, or Puppet won't be able to access your new directory environments.

Accepts a string, such as: `/etc/puppetlabs/code/environments`.

### `sources` 

This setting is used if you are managing more than just Puppet environments, e.g., you are also managing Hiera data in its own control repository. Note that if `sources` is set, you cannot use the `remote` and `r10k_basedir` settings above.

The `sources` setting can include the following subsettings:

* `remote`
* `basedir`
* `prefix`
* `invalid_branches`

This setting accepts a hash, which must be in JSON format, such as:

~~~json
{
  "myorg": {
    "remote": "git://git-server.site/myorg/main-modules",
    "basedir": "/etc/puppetlabs/puppet/environments",
    "prefix": true
  },
  "mysource": {
    "remote": "git://git-server.site/mysource/main-modules",
    "basedir": "/etc/puppetlabs/puppet/environments",
    "prefix": "testing"
  }
}
~~~


Note that r10k raises an error if different sources collide in a single base directory. If you are using multiple sources, use the [`prefix`](#prefix) option to prevent collisions.

#### `remote`

Specifies where the source repository should be fetched from. The remote must be able to be fetched without any interactive input; e.g., you cannot be prompted for usernames or passwords in order to fetch the remote.

Accepts a string that is any valid URL that r10k can clone, such as: 

`git://git-server.site/my-org/main-modules`

#### `basedir`

Specifies the path where environments will be created for this source. This directory is entirely managed by r10k, and any contents that r10k did not put there will be removed.

Note that the `basedir` setting **must match** the `environmentpath` in your puppet.conf file, or Puppet won't be able to access your new directory environments.

#### `prefix`

Allows environment names to be prefixed with a short string specified by this source. If `prefix` is set to 'true', the source's name will be used. If `prefix` is a string, then this string is prefixed to the names of the environments. This prevents collisions when multiple sources are deployed into the same directory.

In the `sources` example above, two "main-modules" environments are set up in the same base directory. The prefix setting is used to differentiate the environments: the first will be named "myorg-main-modules", and the second will be "testing-main-modules".

#### `invalid_branches`

Specifies how branch names that cannot be cleanly mapped to Puppet environments are handled. This option accepts three values:

* 'correct_and_warn' is the default value and replaces non-word characters with underscores and issues a warning.
* 'correct' replaces non-word characters with underscores without warning.
* 'error' ignores branches with non-word characters and issues an error.

~~~json
{
  "mysource": {
    "basedir": "/etc/puppetlabs/puppet/environments",
    "invalid_branches": "error",
    "remote": "git://git-server.site/mysource/main-modules"
  }
}
~~~

## Next Steps

If you don't yet have a Puppetfile, you can [set one up][puppetfile] now. If you have a Puppetfile in your repo already, you can now [run r10k][run]. PE does not automatically run r10k at the end of installation.