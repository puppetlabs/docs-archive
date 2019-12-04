# Customize Code Manager configuration in Hiera

To customize your Code Manager configuration, set parameters with Hiera.

Add Code Manager parameters in the `puppet_enterprise::master::code_manager` class with Hiera.

**Important:**

Do not edit the Code Manager configuration file manually. Puppet manages this configuration file automatically and undoes any manual changes you make.

**Related information**  


[Configure settings with Hiera](config_intro.md#)

## Add Code Manager parameters in Hiera

To customize your Code Manager configuration, add parameters to your control repository hierarchy in the `hieradata/common.yaml` file.

### Procedure

1.  Add the parameter to `hieradata/common.yaml` in the format: `puppet_enterprise::master::code_manager::<parameter>`

    ```
    puppet_enterprise::master::code_manager::deploy_pool_size: 4
    puppet_enterprise::master::code_manager::timeouts_deploy: 300
    
    ```

    The first parameter in this example increases the size of the default worker pool, and the second reduces the maximum time allowed for deploying a single environment.

2.  Run Puppet on the master to apply changes.


### Results

## Configuring post-environment hooks

Configure post-environment hooks to trigger custom actions after your environment deployment.

To configure list of hooks to run after an environment has been deployed, specify the Code Manager`post_environment_hook` setting in Hiera.

This parameter accepts an array of hashes, with the following keys:

-   `url`

-   `use-client-ssl`


For example, to set up Code Manager to updated classes in the console after deploying code to your environments.

```
puppet_enterprise::master::code_manager::post_environment_hooks: 
  - url: 'https://console.yourorg.com:4433/classifier-api/v1/update-classes'    
    use-client-ssl: true 
```

### url

This setting specifies an HTTP URL to send a request to, with the result of the environment deploy. The URL receives a POST with a JSON body with the structure:

```
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

### use-client-ssl

Specifies whether the client SSL configuration should be used for HTTPS connections. If the hook destination is a server using the Puppet CA, then this should be set to `true`; otherwise, it should be set to `false`. Defaults to `false`.

## Configuring garbage collection

By default, Code Manager retains environment deployments in memory for one hour, but you can adjust this by configuring garbage collection. 

To configure the frequency of Code Manager garbage collection, specify the `deploy_ttl` parameter in Hiera. By default, deployments are kept for one hour.

Specify the time as a string using any of the following suffixes:

-   `d` - days

-   `h` - hours

-   `m` - minutes

-   `s` - seconds

-   `ms` - milliseconds


For example, a value of `30d` would configure Code Manager to keep the deployment in memory for 30 days, and a value of `48h` would maintain the deployment in memory for 48 hours.

If the value of `deploy-ttl` is less than the combined values of `timeouts_fetch`, `timeouts_sync`, and `timeouts_deploy`, all completed deployments are retained indefinitely, which might significantly slow the performance of Code Manager over time.

## Configuring Forge settings

To configure how Code Manager downloads modules from the Forge, specify `forge_settings` in Hiera.

This parameter configures where Forge modules should be installed from, and sets a proxy for all Forge interactions. The `forge_settings` parameter accepts a hash with the following values:

-   `baseurl`

-   `proxy`


### baseurl

Indicates where Forge modules should be installed from. Defaults to `https://forgeapi.puppetlabs.com`.

```yaml
puppet_enterprise::master::code_manager::forge_settings: 
  baseurl: 'https://private-forge.mysite'
```

### proxy

Sets the proxy for all Forge interactions.

This setting overrides the global `proxy` setting on Forge operations only. You can set an unauthenticated proxy or an authenticated proxy with either Basic or Digest authentication. See the global `proxy` setting for more information and examples.

```
proxy: 'http://proxy.example.com:3128'
```

## Configuring Git settings

To configure Code Manager to use a private key, a proxy, or multiple repositories with Git, specify the `git_settings` parameter.

The `git_settings` parameter accepts a hash of the following settings:

-   `private-key`

-   `proxy`

-   `repositories`


### private-key

Specifies the file containing the default private key used to access control repositories, such as `/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa`. This file must have read permissions for the `pe-puppet` user. The SSH key cannot require a password. This setting is required, but by default, the value is supplied by the master profile in the console.

### proxy

Sets a proxy specifically for Git operations that use an HTTP\(S\) transport.

This setting overrides the global `proxy` setting on Git operations only. You can set an unauthenticated proxy or an authenticated proxy with either Basic or Digest authentication. To set a proxy for a specific Git repository only, set `proxy` in the `repositories` subsetting of `git_settings`. See the global `proxy` setting for more information and examples.

```
proxy: 'http://proxy.example.com:3128'
```

### repositories

Specifies a list of repositories and their respective private keys. Use this setting if you want to use multiple control repos.

To use multiple control repos, the `sources` setting and the `repositories` setting must match. Accepts the following settings:

-   `remote`: The repository these settings should apply to.
-   `private-key`: The file containing the private key to use for this repository. This file must have read permissions for the `pe-puppet` user.
-   `proxy`: The `proxy` setting allows you to set or override the global proxy setting for a single, specific repository. See the global `proxy` setting for more information and examples.

The `repositories` setting accepts a hash in the following format:

```
repositories:
     - remote: "ssh://jfkennedy@gitserver.puppetlabs.net/repositories/repo_one.git"
       private-key: "/test_keys/jfkennedy"
     - remote: "ssh://whtaft@gitserver.puppetlabs.net/repositories/repo_two.git"
       private-key: "/test_keys/whtaft"
     - remote: "https://git.example.com/git_repos/environments.git"
       proxy: "https://proxy.example.com:3128"
```

## Configuring proxies

To configure proxy servers, use the proxy setting. You can set a global proxy for all HTTP\(S\) operations, for all Git or Forge operations, or for a specific Git repository only.

### proxy

To set a proxy for all operations occurring over an HTTP\(S\) transport, set the global `proxy` setting. You can also set an authenticated proxy with either Basic or Digest authentication.

To override this setting for Git or Forge operations only, set the `proxy` subsetting under the `git_settings` or `forge_settings` parameters. To override for a specific Git repository, set a proxy in the `repositories` list of `git_settings`. To override this setting with no proxy for Git, Forge, or a particular repository, set that specific `proxy` setting to an empty string.

In this example, the first proxy is set up without authentication, and the second proxy uses authentication:

```
proxy: 'http://proxy.example.com:3128'
proxy: 'http://user:password@proxy.example.com:3128'

```

## Configuring sources

If you are managing more than one repository with Code Manager, specify a map of your source repositories.

Use the `source` parameter to specify a map of sources. Configure this setting if you are managing more than just Puppet environments, such as when you are also managing Hiera data in its own control repository.

To use multiple control repos, the `sources` setting and the `repositories` setting must match.

If `sources` is set, you cannot use the global `remote` setting. If you are using multiple sources, use the prefix option to prevent collisions.

The `sources` setting accepts a hash with the following subsettings:

-   remote
-   prefix

```yaml
myorg: 
  remote: "git://git-server.site/myorg/main-modules"
  prefix: true
mysource: 
  remote: "git://git-server.site/mysource/main-modules"
  prefix: "testing"
```

### remote

Specifies the location where the source repository should be fetched from. The remote must be able to be fetched without any interactive input. That is, you cannot be prompted for usernames or passwords in order to fetch the remote.

Accepts a string that is any valid URL that r10k can clone, such as `git://git-server.site/my-org/main-modules`.

### prefix

Specifies a string to prefix to environment names. Alternatively, if `prefix` is set to `true`, the source's name is used. This prevents collisions when multiple sources are deployed into the same directory.

For example, with two "main-modules" environments set up in the same base directory, the prefix setting differentiates the environments: the first is named "myorg-main-modules", and the second is "testing-main-modules".

```
myorg: 
     remote: "git://git-server.site/myorg/main-modules"
     prefix: true
   mysource: 
     remote: "git://git-server.site/mysource/main-modules"
     prefix: "testing"
```

## Code Manager parameters

Code Manager parameters

|Parameter|Description|Value|Default|
|---------|-----------|-----|-------|
|`remote`|The location of the Git control repository. Either the `remote` or `sources` parameters must be specified. If `remote` is already specified in the master profile, that value is used here. If both the `sources` and `remote` keys are specified, the `sources` key overrides `remote`.|A string that is a valid SSH URL for your Git remote.|No default.|
|`authenticate_webhook`|Turns on RBAC authentication for the `/v1/webhook` endpoint.|Boolean.|`true`|
|`cachedir`|The path to the location where Code Manager caches Git repositories.|A valid file path.|`/opt/puppetlabs/server/data/code-manager/cache`.|
|`certname`|The certname of the Puppet signed certs to use for SSL.|A valid certname.|Defaults to `$::clientcert`.|
|`data`|The path to the directory where Code Manager should store internal file content.|A valid file path.|`/opt/puppetlabs/server/data/code-manager`|
|`deploy_pool_size`|Specifies the number of threads in the worker pool; this dictates how many deploy processes can run in parallel.|An integer.|`2`|
|`deploy_ttl`|Specifies the length of time completed deployments are retained before garbage collection. For usage details, see configuring garbage collection.|The time as a string, using any of the following suffixes:

 -   `d` - days

-   `h` - hours

-   `m` - minutes

-   `s` - seconds

-   `ms` - milliseconds


|`1h` \(one hour\)|
|`hostcrl`|The path to the SSL CRL.|A valid file path.

|`$puppet_enterprise::params::hostcrl`|
|`localcacert`|The path to the SSL CA cert.|A valid file path.

|`$puppet_enterprise::params::localcacert`|
|`post_environment_hooks`|A list of hooks to run after an environment has been deployed. Specifies: -   an HTTP URL to send deployment results to,

-   Whether to use client SSL for HTTPS connections


For usage details, see configuring post-environment hooks. |An array of hashes that can include the keys:

-   `url`

-   `use-client-ssl`


|No default.|
|`timeouts_deploy`|Maximum execution time \(in seconds\) for deploying a single environment.|An integer

|`600`|
|`timeouts_fetch`|Maximum execution time \(in seconds\) for updating the control repo state.|An integer.

|`30`|
|`timeouts_hook` |Maximum time \(in seconds\) to wait for a single post-environment hook URL to respond. Used for both the socket connect timeout and the read timeout, so the longest total timeout would be twice the specified value.|An integer.

|`30`|
|`timeouts_shutdown`|Maximum time \(in seconds\) to wait for in-progress deploys to complete when shutting down the service.|An integer.

|`610`|
|`timeouts_sync`|Maximum time \(in seconds\) that a request sent with a `wait` parameter should wait for all compile masters to receive deployed code before timing out.|An integer.

|`60`|
|`webserver_ssl_host`|The host that Code Manager listens on.|An IP address

|`0.0.0.0`|
|`webserver_ssl_port`|The port that Code Manager listens on. Port 8170 must be open if you're using Code Manager.|A port number.

|`8170`|

### r10k specific parameters 

These parameters are specific to r10k, which Code Manager uses in the background.

|Parameter|Description|Value|Default|
|---------|-----------|-----|-------|
|`environmentdir`|The single directory to which Code Manager deploys all sources. If `file_sync_auto_commit` is set to `true`, this defaults to`/etc/puppetlabs/code-staging/environments`. See `file_sync_auto_commit`.|Directory|`/etc/puppetlabs/code-staging/environments`|
|`forge_settings`|Contains settings for downloading modules from the Forge. See [Configuring Forge settings](code_mgr_customizing.md#) for usage details.|Accepts a hash of:

-   `baseurl`

-   `proxy`


|No default.|
|`invalid_branches`|Specifies, for all sources, how branch names that cannot be cleanly mapped to Puppet environments are handled.|-   'correct': Replaces non-word characters with underscores and gives no warning.

-   'error': Ignores branches with non-word characters and issues an error.


|'error'|
|`git_settings`|Configures settings for Git: -   Specifies the file containing the default private key used to access control repositories.

-   Sets or overrides the global proxy setting specifically for Git operations that use an HTTP\(S\) transport.

-   Specifies a list of repositories and their respective private keys.


 See [Configuring Git settings](code_mgr_customizing.md#) for usage details.|Accepts a hash of: -   `private-key`

-   `proxy`

-   `repositories`


|Default `private-key` value as set in console. No other default settings.|
|`proxy`|Configures a proxy server to use for all operations that occur over an HTTP\(S\) transport.

 See [Configuring proxies](code_mgr_customizing.md#) for usage details.

|Accepts: -   A proxy server.

-   An authenticated proxy with Basic or Digest authentication.

-   An empty value.


|No default.|
|`sources`|Specifies a map of sources to be passed to r10k. Use if you are managing more than just Puppet environments.

 See [Configuring sources](code_mgr_customizing.md#) for usage details.

|A hash of: -   `remote`

-   `prefix`


|No default.|

