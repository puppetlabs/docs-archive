---
layout: default
title: "Customize Code Manager configuration in Hiera"
canonical: "/pe/latest/code_mgr_custom.html"
toc: columns
description: "How to customize configuration of Code Manager, a system for managing and deploying Puppet code."
---

[repo]: ./cmgmt_control_repo.html
[puppetfile]: ./cmgmt_puppetfile.html
[code_mgr]: ./code_mgr.html
[r10k]: ./r10k.html
[code_mgr_config]: ./code_mgr_config.html
[code_mgr_custom]: ./code_mgr_custom.html
[code_mgr_webhook]: ./code_mgr_webhook.html
[scripts]: ./code_mgr_scripts.html
[r10k_config]: ./r10k_config.html
[r10k_custom]: ./r10k_custom.html
[r10k_run]: ./r10k_run.html
[r10k_ref]: ./r10k_ref.html
[upgrade]: ./code_mgr_upgrade.html
[hiera]: ./config_intro.html#configure-settings-with-hiera
[filesync]: ./cmgmt_filesync.html
[cli]: ./code_mgr_cli.html


If you need a customized Code Manager configuration, you can set specific parameters with Hiera.

To customize Code Manager configuration, add the parameters you need for the `puppet_enterprise::master::code_manager` class [with Hiera][hiera].

> Warning: Do not edit Code Manager’s configuration file manually. Puppet manages this configuration file automatically and will undo any manual changes you make.

Before you begin, you should already have [enabled and configured][code_mgr_config] Code Manager for Puppet Enterprise.

1. Add the parameter to your control repository hierarchy in the `hieradata/common.yaml` file in the format `puppet_enterprise::master::code_manager::<parameter>`.

   For example:
  
   ```
puppet_enterprise::master::code_manager::deploy_pool_size: 4
puppet_enterprise::master::code_manager::timeouts_deploy: 300
   ```

   The first parameter in the above example increases the size of Code Manager's default worker pool, and the second reduces the maximum time allowed for deploying a single environment.

2. [Run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes) on the Puppet master to apply changes.

## Code Manager parameters

The following parameters are available for Code Manager. Parameters are optional unless otherwise stated.

### `remote`

The location of the Git control repository. Accepts a string that is a valid SSH URL for your Git remote. For example: `'git@<YOUR.GIT.SERVER.COM>:puppet/control.git'`.

Either `remote` or `sources` must be specified. If `remote` is already specified in the master profile, that value is used here. If both the `sources` and `remote` keys are specified, the `sources` key overrides `remote`. 

### `authenticate_webhook`

Turns on RBAC authentication for the `/v1/webhook` endpoint. Defaults to `true`.

### `cachedir`

The path to the location where Code Manager caches Git repositories. Defaults to `/opt/puppetlabs/server/data/code-manager/cache`.

### `certname`

The certname of the Puppet signed certs to use for ssl. Defaults to `$::clientcert`.

### `datadir`

The path to the directory where Code Manager should store internal file content. Defaults to: `/opt/puppetlabs/server/data/code-manager`.

### `deploy_pool_size`

Specifies the number of threads in the worker pool; this dictates how many deploy processes can run in parallel. Accepts an integer. Defaults to 2.

### `deploy-ttl`

Specifies the length of time completed deployments will be retained before garbage collection. Defaults to 1h (one hour). 

You can specify the time as a string using any of the following suffixes:

`d`  - days
`h`  - hours
`m`  - minutes
`s`  - seconds
`ms` - milliseconds

For example, a value of 30d would set the deployment time-to-live to 30 days, and a value of 48h would set the time-to-live to 48 hours.

If the value of `deploy-ttl` is less than the combined values of `timeouts_fetch`, `timeouts_sync`, and `timeouts_deploy`, all completed deployments will be retained indefinitely, which might significantly slow the performance of Code Manager over time. 

### `hostcrl`

The path to the SSL CRL. Defaults to `$puppet_enterprise::params::hostcrl`.

### `localcacert`

The path to the SSL CA cert. Defaults to `$puppet_enterprise::params::localcacert`.

### `post_environment_hooks`

A list of hooks to run after an environment has been deployed. This parameter accepts an array of hashes, such as:

```yaml
#setup code_manager to update classes in the console after environment code deployment 
puppet_enterprise::master::code_manager::post_environment_hooks: 
  - url: 'https://console.yourorg.com:4433/classifier-api/v1/update-classes'    
    use-client-ssl: true 
```

Accepts the following keys:

#### url

An HTTP URL to send a request to, with the result of the environment deploy. The URL receives a POST with a JSON body with the structure:

```json
{
  "deploy-signature":"482f8d3adc76b5197306c5d4c8aa32aa8315694b",
  "file-sync":{
    "environment-commit":"6939889b679fdb1449545c44f26aa06174d25c21",
    "code-commit":"ce5f7158615759151f77391c7b2b8b497aaebce1"},
  "environment":"production",
  "id":3,
  "status":"complete"
}
```

#### use-client-ssl

Whether the client SSL configuration should be used for HTTPS connections. If the hook destination is a server using the Puppet CA, then this should be set to `true`; otherwise, it should be set to `false`. Defaults to `false`.

### `timeouts_deploy`

Maximum execution time (in seconds) for deploying a single environment. Accepts an integer. Defaults to 600.

### `timeouts_fetch`

Maximum execution time (in seconds) for updating the control repo state. Accepts an integer. Defaults to 30.

### `timeouts_hook`

Maximum time (in seconds) to wait for a single post-environment hook URL to respond. This timeout is used for both the socket connect timeout and the read timeout, so the longest total timeout would be twice the specified value. Accepts an integer. Defaults to 30.

### `timeouts_shutdown`

Maximum time (in seconds) to wait for in-progress deploys to complete when shutting down the service. Accepts an integer. Defaults to 610.

### `timeouts_sync`

Maximum time (in seconds) that a request sent with a `wait` parameter should wait for all compile masters to receive deployed code before timing out. Accepts an integer. Defaults to 60.  

### `timeouts_wait`

Maximum time that a request sent with a `wait` parameter should wait for all environments to deploy before timing out. Accepts an integer. Defaults to 700.

### `webserver_ssl_host`

The host Code Manager listens on. Defaults to 0.0.0.0.

### `webserver_ssl_port`

The port Code Manager listens on. Defaults to 8170. Note that this port must be open if you're using Code Manager.

## r10k-specific parameters

**The following parameters are specific to Code Manager's use of r10k:**

### `environmentdir`

The single directory to which Code Manager deploys all sources. If `file_sync_auto_commit` is set to `true`, this defaults to`/etc/puppetlabs/code-staging/environments`. See `file_sync_auto_commit`.

### `forge_settings`

Contains settings for downloading modules from the Puppet Forge. Accepts a hash of the following values:

#### baseurl

Indicates where Forge modules should be installed from. Defaults to `https://forgeapi.puppetlabs.com`.

``` yaml
puppet_enterprise::master::code_manager::forge_settings: 
  baseurl: 'https://private-forge.mysite'
```
### `invalid_branches`

Specifies, for all sources, how branch names that cannot be cleanly mapped to Puppet environments are handled. This option accepts two values:

* 'correct' replaces non-word characters with underscores without giving a warning.
* 'error' ignores branches with non-word characters and issues an error. This is the default value for this setting.

#### proxy

Sets or overrides the global proxy setting for all Forge interactions. See the global `proxy` setting below for more information and examples.

### `git_settings`

Contains Git-specific settings. Accepts a hash of the following settings:

#### private-key

Required, but can be supplied by the master profile. Specifies the file containing the default private key used to access control repositories, such as `/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa`. This file must have read permissions for the `pe-puppet` user. The SSH key cannot require a password.

#### proxy

Sets or overrides the global proxy setting specifically for Git operations that use an HTTP(S) transport. To See the global `proxy` setting below for more information and examples.

#### repositories

A list of repositories and their respective private keys. Use this setting if you want to use multiple control repos. To use multiple control repos, the `sources` setting and the `repositories` setting must match. Accepts the following settings:

* `remote`: The repository these settings should apply to.
* `private-key`: The file containing the private key to use for this repository. This file must have read permissions for the `pe-puppet` user.
* `proxy`: The `proxy` setting allows you to set or override the global proxy setting for a single, specific repository. See the global `proxy` setting below for more information and examples.

The `repositories` setting accepts a hash in the following format:

```yaml
repositories:
  - remote: "ssh://jfkennedy@gitserver.puppetlabs.net/repositories/repo_one.git"
    private-key: "/test_keys/jfkennedy"
  - remote: "ssh://whtaft@gitserver.puppetlabs.net/repositories/repo_two.git"
    private-key: "/test_keys/whtaft"
  - remote: "https://git.example.com/git_repos/environments.git"
    proxy: "https://proxy.example.com:3128"
```

### `private_key`

**Deprecated.** Set the `private-key` in the [`git_settings`](#gitsettings) parameter instead.

### `proxy`

Configures a proxy server to use for all operations that occur over an HTTP(S) transport. To override this setting for Git or Forge operations only, set the `proxy` parameter under [`git_settings`](#gitsettings) or [`forge_settings`](#forgesettings). To override for a specific Git repository, set a proxy in the `repositories` list of `git_settings`. To override this setting with no proxy for Git, Forge, or a particular repository, set that specific `proxy` setting to an empty string.

```yaml
proxy: 'http://proxy.example.com:3128'
```

Code Manager also supports authenticated proxies with either Basic or Digest authentication:

```yaml
proxy: 'http://user:password@proxy.example.com:3128'
```

### `sources`

Specifies a map of sources to be passed to r10k. This setting is used if you are managing more than just Puppet environments, such as when you are also managing Hiera data in its own control repository. To use multiple control repos, the `sources` setting and the `repositories` setting must match. Note that if `sources` is set, you cannot use the global `remote` and `r10k_basedir` settings above.

The `sources` setting can include the following subsettings:

* [remote](#remote)
* [prefix](#prefix)

This setting accepts a hash such as:

```yaml
myorg: 
  remote: "git://git-server.site/myorg/main-modules"
  prefix: true
mysource: 
  remote: "git://git-server.site/mysource/main-modules"
  prefix: "testing"
```

Note that r10k raises an error if different sources collide in a single base directory. If you are using multiple sources, use the [prefix](#prefix) option to prevent collisions.

#### remote

Specifies where the source repository should be fetched from. The remote must be able to be fetched without any interactive input. That is, you cannot be prompted for usernames or passwords in order to fetch the remote.

Accepts a string that is any valid URL that r10k can clone, such as: 

`git://git-server.site/my-org/main-modules`

#### prefix

Allows environment names to be prefixed with this string. Alternatively, if `prefix` is set to `true`, the source's name is used. This prevents collisions when multiple sources are deployed into the same directory.

In the `sources` example above, two "main-modules" environments are set up in the same base directory. The prefix setting is used to differentiate the environments: the first will be named "myorg-main-modules", and the second will be "testing-main-modules". This setting accepts a hash such as:

```yaml
myorg: 
  remote: "git://git-server.site/myorg/main-modules"
  prefix: true
mysource: 
  remote: "git://git-server.site/mysource/main-modules"
  prefix: "testing"
```

## Next steps

After you've got Code Manager configured, you can deploy environments with Code Manager from the [command line][cli], or set up a deployment trigger for Code Manager with a [webhook][code_mgr_webhook] or a [custom script][scripts].
