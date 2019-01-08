---
layout: default
title: "Customizing Code Manager Configuration in Hiera"
canonical: "/pe/latest/code_mgr_custom.html"
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


If you need a more highly customized Code Manager configuration, you can configure Code Manager by setting parameters for the `puppet_enterprise::master::code_manager` class [via Hiera][hiera].
 
To set the `code_manager` class parameters via Hiera, add keys to your control repository hierarchy in the `hieradata/common.yaml` file. To do this, set `puppet_enterprise::master::code_manager::<parameter>` for the specific parameter. For example:

~~~
puppet_enterprise::master::code_manager::deploy_pool_size: 4
puppet_enterprise::master::code_manager::timeouts_deploy: 300
~~~

The first key increases the size of Code Manager's default worker pool, and the second reduces the maximum time allowed for deploying a single environment.

> Warning: Do not edit Code Manager’s configuration file manually. Puppet manages this configuration file automatically and will undo any manual changes you make. 

After you add keys to Hiera, run Puppet on the master to apply changes.

## Add `code_manager` Keys

The following parameters are available. Parameters are optional unless otherwise stated.

* `remote`: The location of the Git control repository. Accepts a string that is a valid URL for your Git remote. For example: `'git@<YOUR.GIT.SERVER.COM>:puppet/control.git'`. Either `remote` or `sources` must be specified. If `remote` is already specified in the master profile, that value is used here. The both the `sources` and `remote` keys are specified, the `sources` key overrides `remote`. 
* `authenticate_webhook`: Turns on RBAC authentication for the `/v1/webhook` endpoint. Defaults to true.
* `cachedir` - The path to where code-manager caches Git repositories. Defaults to `/opt/puppetlabs/server/data/code-manager/cache`.
* `certname` - The certname of the Puppet signed certs to use for ssl. Defaults to `$::clientcert`.
* `datadir`: The path to the directory where code-manager should store internal file content. Defaults to: '/opt/puppetlabs/server/data/code-manager'.
* `deploy-pool-size`: Specifies the number of threads in the worker pool; this dictates how many deploy processes can run in parallel. Accepts an integer. Defaults to 2.
* `hostcrl` - The path to the ssl crl. Defaults to `$puppet_enterprise::params::hostcrl`.
* `localcacert` - The path to the ssl ca cert. Defaults to `$puppet_enterprise::params::localcacert`.
* `timeouts_deploy`: Maximum execution time (in seconds) for deploying a single environment. Accepts an integer. Defaults to 600.
* `timeouts_fetch`: Maximum execution time (in seconds) for updating the control repo state. Accepts an integer. Defaults to 30.
* `timeouts_hook`: Maximum time (in seconds) to wait for a single post-environment hook URL to respond. This timeout is used for both the socket connect timeout and the read timeout, so the longest total timeout would be twice the specified value. Accepts an integer. Defaults to 30.
* `timeouts_shutdown`: Maximum time (in seconds) to wait for in-progress deploys to complete when shutting down the service. Accepts an integer. Defaults to 610.
* `timeouts_wait`: Maximum time that a request sent with a `wait` parameter should wait for each environment before timing out. Accepts an integer. Defaults to 700.
* `webserver_ssl_host` - The host code-manager listens on. Defaults to 0.0.0.0.
* `webserver_ssl_port` = The port code-manager listens on. Defaults to 8170. Note that this port must be open if you're using code-manager.

These parameters are specific to Code Manager's use of r10k:

* `environmentdir`: The single directory that Code Manager deploys all sources to. If `file_sync_auto_commit` is set to true, this defaults to`/etc/puppetlabs/code-staging/environments`. See `file_sync_auto_commit`.
* `file_sync_repo_id` The repo that code-manager should publish environments to. Defaults to 'puppet-code'.
* `file_sync_auto_commit` Automatically commit each environment to file-sync after deploying it. Switching autocommit off changes the default environmentdir to `/etc/puppetlabs/code/environments`. If you have already published though code-manager you will have to delete the `.git` file from each environment before code-manager will be able to deploy new code. Defaults to true.
* `private_key`: Required, but can be supplied by the master profile. The path to the file containing the private key used to access all Git repositories. Accepts a string, such as `'/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa'`. This file must have read permissions for the `pe-puppet` user.
* `post_environment_hooks`: A list of hooks to run after an environment has been deployed. This parameter is an array of hashes, such as:

  ~~~yaml
#setup code_manager to update classes in the console after environment code deployment 
puppet_enterprise::master::code_manager::post_environment_hooks: 
  - url: 'https://console.yourorg.com:4433/classifier-api/v1/update-classes'    
    use-client-ssl: true 
  ~~~
    
    Each hook is a map that can have the following keys.

  * `url`: A HTTP URL to send a request to with the result of the environment deploy. The URL will receive a POST with a JSON body with the structure:

    ~~~
    {
      "deploy-signature":"482f8d3adc76b5197306c5d4c8aa32aa8315694b",
      "file-sync":{
        "environment-commit":"6939889b679fdb1449545c44f26aa06174d25c21",
        "code-commit":"ce5f7158615759151f77391c7b2b8b497aaebce1"},
      "environment":"production",
      "id":3,
      "status":"complete"
    }
    ~~~
  * `use-client-ssl`: Whether the client SSL configuration should be used for HTTPS connections. If the hook destination is a server using the Puppet CA then this should be set to true, otherwise it should be set to false. Defaults to false.
* `sources`: Specifies a map of sources to be passed to r10k. This setting is used if you are managing more than just Puppet environments, such as when you are also managing Hiera data in its own control repository. Note that if `sources` is set, you cannot use the `remote` settings above.

  The `sources` setting can include the following subsettings:

   * `remote`
   * `prefix`

  This setting accepts a hash, which must be in JSON format, such as:

        {
          "myorg": {
            "remote": "git://git-server.site/myorg/main-modules",
            "prefix": true
          },
          "mysource": {
            "remote": "git://git-server.site/mysource/main-modules",
            "prefix": "testing"
          }
        }


## Next Steps

After you've got Code Manager configured, you're ready to set up a deployment trigger for Code Manager with a [webhook][code_mgr_webhook] or a [custom script][scripts].
