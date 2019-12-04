---
layout: default
title: "Triggering Code Manager on the command line"
canonical: "/pe/latest/code_mgr_cli.html"
description: "Triggering Code Manager, a system for managing and deploying Puppet code, with the command line tool."
---

[repo]: ./cmgmt_control_repo.html
[puppetfile]: ./cmgmt_puppetfile.html
[code_mgr]: ./code_mgr.html
[r10k]: ./r10k.html
[code_mgr_config]: ./code_mgr_config.html
[code_mgr_custom]: ./code_mgr_custom.html
[code_mgr_webhook]: ./code_mgr_webhook.html
[scripts]: ./code_mgr_scripts.html
[filesync]: ./cmgmt_filesync.html
[install_client]: ./install_pe_client_tools.html
[config_command]: #deploy-with-command-line-configuration-settings
[authenticate]: ./code_mgr_config.html#set-up-authentication-for-code-manager

The `puppet code` command allows you to trigger Code Manager from the command line to deploy your environments. This page describes how to set up and use the command, and how to customize its configuration. **These instructions assume that you have already [enabled and configured][code_mgr_config] Code Manager.**

## Installing and configuring `puppet code`

Puppet Enterprise automatically installs and configures the `puppet code` command on masters as part of the PE client tools package. The global configuration settings are in a JSON file at `/etc/puppetlabs/client-tools/puppet-code.conf`. The default configuration file looks something like:

```json
{
"cacert": "/etc/puppetlabs/puppet/ssl/certs/ca.pem",
"token-file": "~/.puppetlabs/token",
"service-url": "https://<PUPPET MASTER HOSTNAME>:8170/code-manager"
 }
```

> #### Windows paths
> 
> The global configuration file on Windows systems is at `C:\ProgramData\PuppetLabs\client-tools\puppet-code.conf`.
> The default configuration file on Windows looks something like:
>
> ```json
> {
> "cacert": "C:\\ProgramData\\PuppetLabs\\puppet\\etc\\ssl\\certs\\ca.pem",
> "token-file": "C:\\Users\\<username>\\.puppetlabs\\token",
> "service-url": "https://<PUPPET MASTER HOSTNAME>:8170/code-manager"
>  }
> ```

On a PE-managed machine, Puppet manages this file for you. Do not manually edit this file, because Puppet overwrites your new values the next time it runs.

You can also set up `puppet code` on an agent node or on a workstation not managed by PE. And on any machine, you can set up user-specific config files. See [Advanced configuration](#advanced-configuration) for instructions.

When you run a `puppet code` command, you can override existing configuration settings on a case-by-case basis, either by specifying an alternative config file, or by specifying particular config settings directly in the command. See the examples of [deploying environments](#deploying-environments-with-puppet-code) with `puppet code`.


#### About configuration precedence

If no other configuration is specified, `puppet code` uses the settings in the  global configuration file. User-specific configuration files override the global configuration file.

If you specify a configuration file on the command line, Puppet temporarily uses that configuration file **only** and does not read the global or user-specific config files at all. Finally, if you specify individual configuration options directly [on the command line][config_command], those options temporarily take precedence over *any* configuration file settings.


## Deploying environments with `puppet code`

To deploy environments with the `puppet code` command, use the `deploy` action, either with the name of a single environment or with the `--all` flag.

The `deploy` action deploys the environments, but returns only deployment queuing results by default. To get the results of the deployment, add the `--wait` flag. This gives you the deployment signature, which is the commit SHA of the control repo used to deploy the environment. The output also includes two other SHAs that indicate that file sync is aware that the environment has been newly deployed to the code-staging directory.

To temporarily override default, global, and user-specific configuration settings, specify the following configuration options on the command line:

* [`--cacert`](#cacert)
* [`--token-file`](#token-file), `-t`
* [`--service-url`](#service-url)

Or specify a custom `puppet-code.conf` configuration file by using the `--config-file` option.

> #### Running `puppet code` on Windows
>
>If you're running these commands from a managed or non-managed Windows workstation, you must specify the full path to the command. For example:
>
>`C:\Program Files\Puppet Labs\Client\bin\puppet code deploy mytestenvironment --wait`

### Deploy a single environment

```
puppet code deploy mytestenvironment --wait
```

### Deploy all environments

```
puppet code deploy --all --wait
```

The examples above deploy the specified environments, and then return deployment results with control repo commit SHAs for each environment. The output is in JSON format, so you can pipe it through a JSON tool to improve readability.

### Deploy with a custom configuration file

```
puppet code --config-file ~/.puppetlabs/myconfigfile/puppet-code.conf deploy --all
```

This example deploys all environments using the configuration settings in the specified file. For missing settings, it uses defaults and does not fall back to the global or user-specific configurations.

### Deploy with command-line configuration settings

```
puppet code --service-url "https://puppet.example.com:8170/code-manager" deploy mytestenvironment
```

This example deploys 'mytestenvironment' using global or user-specific config settings (if set), except for --service-url, for which it uses the provided value.

## Reference: `puppet code` command

Usage: `puppet code [global options] <action> [action arguments]`

### Global options

#### `--help`, `-h`

Prints usage information for `puppet code`.

#### `--version`, `-V`

Prints the application version.

#### `--log-level`, `-l`

Sets the log verbosity. It accepts one of the following log levels as an argument.

Supported log levels:

* none
* trace
* debug
* info
* warn
* error
* fatal

#### `--config-file`, `-c`

Accepts an argument specifying the location of a `puppet-code.conf` file that takes precedence over all other existing `puppet-code.conf` files.

#### `--cacert`

Accepts an argument specifying the location of the Puppet CA certificate. Overrides any configuration file `cacert` settings.

#### `--token-file`, `-t`

Accepts an argument specifying the location of the authentication token for Code Manager. Overrides any configuration file `token-file` settings.

#### `--service-url`

Accepts an argument specifying the base URL of the Code Manager service. Overrides any configuration file `service-url` settings.

### `puppet code` actions

#### `print-config`

The `puppet code print-config` action prints out the resolved `puppet code` configuration. This allows you to see the final results of the configuration files and settings being applied to `puppet code` before you use the command to deploy environments.

#### `deploy`

The `puppet code deploy` action runs remote code deployments with the Code Manager service. It accepts either the name of a single environment or the `--all` flag. This command returns only deployment queuing results by default.

The `deploy` action accepts the following options:

* `--wait`, `-w`: The `--wait` flag causes `puppet code deploy` to start a deployment, wait for the deployment to complete, and then return the deployment signature with control repo commit SHAs for each environment. The output also includes two other SHAs that indicate that file sync is aware that the environment has been newly deployed to the code-staging directory. This output is in JSON format.
* `--all`: The `--all` flag causes `puppet code deploy` to start deployments for all Code Manager environments.
* `--format`, `-F`: Specifies the output format. Currently, the default and only supported value is 'json'.

## Advanced configuration

### Installing and configuring `puppet code` on agents and workstations

To use `puppet code` on an agent node or on a workstation that is not managed by PE:

1. Install the client tools package. See the [client tools documentation][install_client] for instructions.
2. Create a config file called `puppet-code.conf` in the `/etc/puppetlabs/client-tools` folder. For Windows systems, this file location is `C:\ProgramData\PuppetLabs\client-tools\puppet-code.conf`.
3. In the `puppet-code.conf` file, specify your [configuration settings](#configuration-settings) in JSON format, like this example:

```json
{
"cacert": "/etc/puppetlabs/puppet/ssl/certs/ca.pem",
"token-file": "~/.puppetlabs/token",
"service-url": "https://<PUPPET MASTER HOSTNAME>:8170/code-manager"
}
```

For Windows systems, use the [Windows paths](#windows-paths) listed above.

### Configuring `puppet code` for different users

If you want a user to have their own custom configuration for the `puppet code` command, create a `puppet-code.conf` JSON file in that user’s `~/.puppetlabs/client-tools/` folder, and [customize its cacert, token-file, and service-url settings](#configuration-settings).

For Windows systems, the default user config file location is `%USERPROFILE%\.puppetlabs\client-tools\puppet-code.conf`.

### Configuration settings

#### `cacert`

Specifies the SSL CA certificate to use when connecting to the Code Manager service over SSL. Set this to the location of the Puppet CA certificate.

Default value: `/etc/puppetlabs/puppet/ssl/certs/ca.pem`.

Windows default: `C:\ProgramData\PuppetLabs\puppet\etc\ssl\certs\ca.pem`.

#### `token-file`

Specifies the file containing the authentication token for Code Manager.

Default value: `~/.puppetlabs/token`.

Windows default: `C:\Users\<username>\.puppetlabs\token`

#### `service-url`

Specifies the base URL of the Code Manager service.

Default value: `https://<PUPPET MASTER HOSTNAME>:8170/code-manager`.

