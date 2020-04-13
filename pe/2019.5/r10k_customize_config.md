# Customizing r10k configuration

If you need a customized r10k configuration, you can set specific parameters with Hiera.

To customize your configuration, add keys to your control repository hierarchy in the `data/common.yaml` file in the format `pe_r10k::<parameter>`.

In this example, the first parameter specifies where to store the cache of your Git repos, and the second sets where the source repository should be fetched from.

```
pe_r10k::cachedir: /var/cache/r10k
pe_r10k::remote: git://git-server.site/my-org/main-modules
```

**Remember:** After changing any of these settings, run r10k on the command line to deploy your environments. PE does not automatically run r10k at the end of installation.

**Related information**  


[Configure settings with Hiera](config_intro.md#)

## Add r10k parameters in Hiera

To customize your r10k configuration, add parameters to your control repository hierarchy in the `data/common.yaml` file.

### Procedure

1.  Add the parameter in the `data/common.yaml` file in the format `pe_r10k::<parameter>`.

2.  Run Puppet on the master to apply changes.


## Configuring Forge settings

To configure how r10k downloads modules from the Forge, specify `forge_settings` in Hiera.

This parameter configures where Forge modules should be installed from, and sets a proxy for all Forge interactions. The `forge_settings` parameter accepts a hash with the following values:

-   `baseurl`

-   `proxy`


### baseurl

Indicates where Forge modules should be installed from. Defaults to `https://forgeapi.puppetlabs.com`.

```
pe_r10k::forge_settings:
  baseurl: 'https://private-forge.mysite'
```

### proxy

Sets the proxy for all Forge interactions.

This setting overrides the global `proxy` setting on Forge operations only. You can set an unauthenticated proxy or an authenticated proxy with either Basic or Digest authentication. See the global `proxy` setting for more information and examples.

```
proxy: 'http://proxy.example.com:3128'
```

## Configuring purge levels

The `purge_levels` setting controls which unmanaged content r10k purges during a deployment.

This setting accepts an array of strings specifying what content r10k purges during code deployments. Available values are:

-   `deployment`

-   `environment`

-   `puppetfile`

-   `purge_whitelist`


The default value for this setting is `['deployment', 'puppetfile']`. With these values, r10k purges:

-   Any content that is not managed by the sources declared in the `remote` or `sources` settings. 

-   Any content that is not declared in the Puppetfile, but is found in a directory managed by the Puppetfile.


```
deploy:
  purge_levels: [ 'deployment', 'environment', 'puppetfile' ]
```

### deployment

After each deployment, in the configured basedir, r10k recursively removes content that is not managed by any of the sources declared in the `remote` or `sources` parameters.

Disabling this level of purging could cause the number of deployed environments to grow without bound, because deleting branches from a control repo would no longer cause the matching environment to be purged.

### environment

After a given environment is deployed, r10k recursively removes content that is neither committed to the control repo branch that maps to that environment, nor declared in a Puppetfile committed to that branch.

With this purge level, r10k loads and parses the Puppetfile for the environment even if the `--puppetfile` flag is not set. This allows r10k to check whether or not content is declared in the Puppetfile. However,  Puppetfile content is deployed only if the environment is new or the `--puppetfile` flag is set.

If r10k encounters an error while evaluating the Puppetfile or deploying its contents, no environment-level content is purged.

### puppetfile

After Puppetfile content for a given environment is deployed, r10k recursively removes content in any directory managed by the Puppetfile, if that content is not also declared in that Puppetfile.

Directories considered to be managed by a Puppetfile include the configured `moduledir` \(which defaults to "modules"\), as well as any alternate directories specified as an `install_path` option to any Puppetfile content declarations.

### purge\_whitelist

The `purge_whitelist` setting exempts the specified filename patterns from being purged.

This setting affects `environment` level purging only. Any given value must be a list of shell style filename patterns in string format.

See the [Ruby documentation](https://ruby-doc.org/core-2.2.0/File.html) for the `fnmatch` method for more details on valid patterns. Both the `FNM_PATHNAME` and `FNM_DOTMATCH` flags are in effect when r10k considers the whitelist.

Patterns are relative to the root of the environment being purged and, by default, **do not match recursively**. For example, a whitelist value of `*myfile*` would preserve only a matching file at the root of the environment. To preserve the file throughout the deployed environment, you would need to use a recursive pattern such as `**/*myfile*`.

Files matching a whitelist pattern might still be removed if they exist in a folder that is otherwise subject to purging. In this case, use an additional whitelist rule to preserve the containing folder.

```yaml
deploy:
  purge_whitelist: [ 'custom.json', '**/*.xpp' ]
```

## Locking r10k deployments

The `deploy: write_lock` setting allows you to temporarily disallow r10k code deploys without completely removing the r10k configuration.

This `deploy` subsetting is useful to prevent r10k deployments at certain times or to prevent deployments from interfering with a common set of code that might be touched by multiple r10k configurations.

```
deploy:
  write_lock: "Deploying code is disallowed until the next maintenance window." 
```

## Configuring sources

If you are managing more than one repository with r10k, specify a map of your source repositories.

Use the `source` parameter to specify a map of sources. Configure this setting if you are managing more than just Puppet environments, such as when you are also managing Hiera data in its own control repository.

To use multiple control repos, the `sources` setting and the `repositories` setting must match.

If `sources` is set, you cannot use the global `remote` and `r10k_basedir` settings. Note that r10k raises an error if different sources collide in a single base directory. If you are using multiple sources, use the `prefix` option to prevent collisions.

This setting accepts a hash with the following subsettings:

-   `remote`

-   `basedir`

-   `prefix`

-   `invalid_branches`


```yaml
myorg: 
  remote: "git://git-server.site/myorg/main-modules"
  basedir: "/etc/puppetlabs/puppet/environments"
  prefix: true
  invalid_branches: 'error'
mysource: 
  remote: "git://git-server.site/mysource/main-modules"
  basedir: "/etc/puppetlabs/puppet/environments"
  prefix: "testing"
  invalid_branches: 'correct_and_warn'
```

### remote

Specifies where the source repository should be fetched from. The remote must be able to be fetched without any interactive input. That is, you cannot be prompted for usernames or passwords in order to fetch the remote.

Accepts a string that is any valid URL that r10k can clone, such as:

`git://git-server.site/my-org/main-modules`

### basedir

Specifies the path where environments are created for this source. This directory is entirely managed by r10k, and any contents that r10k did not put there are removed.

Note that the `basedir` setting **must match** the `environmentpath` in your puppet.conf file, or Puppet won't be able to access your new directory environments.

### prefix

Allows environment names to be prefixed with this string. Alternatively, if `prefix` is set to true, the source's name is used. This prevents collisions when multiple sources are deployed into the same directory.

In the `sources` example above, two "main-modules" environments are set up in the same base directory. The prefix setting is used to differentiate the environments: the first is named "myorg-main-modules", and the second is "testing-main-modules".

### ignore\_branch\_prefixes

Causes branches from a source which match any of the prefixes listed in the setting to be ignored. The match can be full or partial. On deploy, each branch in the repo has its name tested against all prefixes listed in `ignore_branch_prefixes` and, if the prefix is found, then an environment is not deployed for this branch.

The setting is a list of strings. In the following example, branches in "mysource" with the prefixes "test" and "dev" are not deployed.

```
---
sources:
  mysource:
    basedir: '/etc/puppet/environments'
    ignore_branch_prefixes:
      - 'test'
      - 'dev'
```

If `ignore_branch_prefixes` is not specified, then all branches are deployed.

### invalid\_branches

Specifies how branch names that cannot be cleanly mapped to Puppet environments are handled. This option accepts three values:

-   'correct\_and\_warn' is the default value and replaces non-word characters with underscores and issues a warning.
-   'correct' replaces non-word characters with underscores without warning.
-   'error' ignores branches with non-word characters and issues an error.

## Configuring the r10k base directory

The r10k base directory specifies the path where environments are created for this source.

This directory is entirely managed by r10k, and any contents that r10k did not put there are removed. If `r10k_basedir` is not set, it uses the default `environmentpath` in your `puppet.conf` file. If `r10k_basedir` is set, you cannot set the `sources` parameter.

Note that the `r10k_basedir` setting **must match** the `environmentpath` in your `puppet.conf` file, or Puppet won't be able to access your new directory environments.

Accepts a string, such as: `/etc/puppetlabs/code/environments`.

## Configuring r10k Git settings

To configure r10k to use a specific Git provider, a private key, a proxy, or multiple repositories with Git, specify the `git_settings` parameter.

The r10k`git_settings` parameter accepts a hash of the following settings:

-   `private_key`

-   `proxy`

-   `repositories`

-   `provider`


Contains settings specific to Git. Accepts a hash of values, such as:

```yaml
pe_r10k::git_settings:
  provider: "rugged"
  private_key: "/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa"
  username: "git"
```

### private\_key

Specifies the file containing the default private key used to access control repositories, such as `/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa`. This file must have read permissions for the `pe-puppet` user. The SSH key cannot require a password. This setting is required, but by default, the value is supplied by the master profile in the console.

### proxy

Sets a proxy specifically for Git operations that use an HTTP\(S\) transport.

This setting overrides the global `proxy` setting on Git operations only. You can set an unauthenticated proxy or an authenticated proxy with either Basic or Digest authentication. To set a proxy for a specific Git repository only, set `proxy` in the `repositories` subsetting of `git_settings`. See the global `proxy` setting for more information and examples.

```
proxy: 'http://proxy.example.com:3128'
```

### repositories

Specifies a list of repositories and their respective private keys. Use this setting if you want to use multiple control repos.

To use multiple control repos, the `sources` setting and the `repositories` setting must match. Accepts an array of hashes with the following keys:

-   `remote`: The repository these settings should apply to.
-   `private_key`: The file containing the private key to use for this repository. This file must have read permissions for the `pe-puppet` user. This setting overrides the global `private_key` setting.
-   `proxy`: The `proxy` setting allows you to set or override the global proxy setting for a single, specific repository. See the global `proxy` setting for more information and examples.

```
pe_r10k::git_settings:
  repositories:
    - remote: "ssh://tessier-ashpool.freeside/protected-repo.git"
      private_key: "/etc/puppetlabs/r10k/ssh/id_rsa-protected-repo-deploy-key"
    - remote: "https://git.example.com/my-repo.git"
      proxy: "https://proxy.example.com:3128"
```

### username

Optionally, specify a username when the Git remote URL does not provide a username.

## Configuring proxies

To configure proxy servers, use the `proxy` setting. You can set a global proxy for all HTTP\(S\) operations, for all Git or Forge operations, or for a specific Git repository only.

### proxy

To set a proxy for all operations occurring over an HTTP\(S\) transport, set the global `proxy` setting. You can also set an authenticated proxy with either Basic or Digest authentication.

To override this setting for Git or Forge operations only, set the `proxy` subsetting under the `git_settings` or `forge_settings` parameters. To override for a specific Git repository, set a proxy in the `repositories` list of `git_settings`. To override this setting with no proxy for Git, Forge, or a particular repository, set that specific `proxy` setting to an empty string.

In this example, the first proxy is set up without authentication, and the second proxy uses authentication:

```
proxy: 'http://proxy.example.com:3128'
proxy: 'http://user:password@proxy.example.com:3128'

```

### r10k proxy defaults and logging

By default, r10k looks for and uses the first environment variable it finds in this list:

-   HTTPS\_PROXY

-   https\_proxy

-   HTTP\_PROXY

-   http\_proxy


If no proxy setting is found in the environment, the global `proxy` setting defaults to no proxy.

The proxy server used is logged at the "debug" level when r10k runs.

## Configuring postrun commands

To configure a command to be run after all deployment is complete, add the `postrun` parameter.

The `postrun` optional command to be run after r10k finishes deploying environments. The command must be an array of strings to be used as an argument vector. You can set this parameter only one time.

```yaml
  postrun: ['/usr/bin/curl', '-F', 'deploy=done', 'http://my-app.site/endpoint']
```

## r10k parameters

The following parameters are available for r10k. Parameters are optional unless otherwise stated.

|Parameter|Description|Value|Default|
|---------|-----------|-----|-------|
|`cachedir`|Specifies the path to the directory where you want the cache of your Git repos to be stored.|A valid directory path|`/var/cache/r10k`|
|`deploy`|Controls how r10k code deployments behave.

See [Configuring purge levels](r10k_customize_config.md#) and [Locking deployments](r10k_customize_config.md#) for usage details.

|Accepts settings for: -   `purge_level`

-   `write_lock`


|-   `purge_level: ['deployment', 'puppetfile']`

-   `write_lock:` Not set by default .


|
|`forge_settings`|Contains settings for downloading modules from the Puppet Forge. See [Configuring Forge settings](r10k_customize_config.md#) for usage details.

|Accepts a hash of: -   `baseurl`

-   `proxy`


|No default.|
|`git_settings`|Configures Git settings: Git provider, private key, proxies, and repositories. See [Configuring Git settings](r10k_customize_config.md#) for usage details.

|Accepts a hash of: -   `provider`

-   `private_key`

-   `proxy`

-   `repositories`


|No default.|
|`proxy`|Configures a proxy server to use for all operations that occur over an HTTP\(S\) transport.

See [Configuring proxies](r10k_customize_config.md#) for usage details.

|Accepts a string specifying a URL to proxy server, without authentication, or with Basic or Digest authentication.|No default set.|
|`postrun`|An optional command to be run after r10k finishes deploying environments.

|An array of strings to use as an argument vector.|No default set.|
|`remote`|Specifies where the source repository should be fetched from. The remote cannot require any prompts, such as for usernames or passwords. If `remote` is set, you cannot use `sources`.

|Accepts a string that is any valid SSH URL that r10k can clone.|By default, uses the `r10k_remote`value set in the console.|
|`r10k_basedir`|Specifies the path where environments are created for this source. See [Configuring the base directory](r10k_customize_config.md#) for usage details.

|A valid file path, which must match the value of `environmentpath` in your `puppet.conf`file.|Uses the value of the `environmentpath` in your `puppet.conf` file.|
|`sources`|Specifies a map of sources to be passed to r10k. Use if you are managing more than just Puppet environments.

See [Configuring sources](r10k_customize_config.md#) for usage details.

|A hash of: -   `basedir`

-   `remote`

-   `prefix`

-   `ignore_branch_prefixes`


|No default.|

