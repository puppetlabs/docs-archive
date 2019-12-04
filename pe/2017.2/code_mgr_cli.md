---
layout: default
title: "Triggering Code Manager on the command line"
canonical: "/pe/latest/code_mgr_cli.html"
description: "Triggering Code Manager, a system for managing and deploying Puppet code, with the command line tool."
---

[code_mgr_config]: ./code_mgr_config.html

[repo]: ./cmgmt_control_repo.html
[puppetfile]: ./cmgmt_puppetfile.html
[code_mgr]: ./code_mgr.html
[r10k]: ./r10k.html
[code_mgr_custom]: ./code_mgr_custom.html
[code_mgr_webhook]: ./code_mgr_webhook.html
[scripts]: ./code_mgr_scripts.html
[filesync]: ./cmgmt_filesync.html
[install_client]: ./install_pe_client_tools.html
[config_command]: #deploy-with-command-line-configuration-settings
[authenticate]: ./code_mgr_config.html#set-up-authentication-for-code-manager

The `puppet-code` command allows you to trigger Code Manager from the command line to deploy your environments.

Before you begin, you should already have [enabled and configured][code_mgr_config] Code Manager.

## Installing and configuring `puppet-code`

Puppet Enterprise (PE) automatically installs and configures the `puppet-code` command on your Puppet masters as part of the included PE client tools package.

The global configuration settings are in a JSON file at `/etc/puppetlabs/client-tools/puppet-code.conf`. The default configuration file looks something like:

```json
{
"cacert": "/etc/puppetlabs/puppet/ssl/certs/ca.pem",
"token-file": "~/.puppetlabs/token",
"service-url": "https://<PUPPET MASTER HOSTNAME>:8170/code-manager"
 }
```

> #### Windows paths
>
> The global `puppet-code` configuration file on Windows systems is located at `C:\ProgramData\PuppetLabs\client-tools\puppet-code.conf`.
>
> The default configuration file on Windows looks something like:
>
> ```json
> {
> "cacert": "C:\\ProgramData\\PuppetLabs\\puppet\\etc\\ssl\\certs\\ca.pem",
> "token-file": "C:\\Users\\<username>\\.puppetlabs\\token",
> "service-url": "https://<PUPPET MASTER HOSTNAME>:8170/code-manager"
>  }
> ```

On a PE-managed machine, Puppet manages this file for you. **Do not manually edit this file, because Puppet will overwrite your new values the next time it runs.**

Additionally, you can set up `puppet-code` on an agent node or on a workstation not managed by PE. And on any machine, you can set up user-specific config files. See advanced configuration of `puppet-code` for instructions.

You can also override existing configuration settings on a case-by-case basis on the command line. When you deploy environments with the `puppet-code deploy` command, you can specify either an alternative config file or particular config settings directly in the command. See deploying environments with puppet code for examples.

Related topics:

* [Advanced configuration of `puppet-code`](#advanced-configuration-of-puppet-code).
* [Deploying environments with `puppet-code`](#deploying-environments-with-puppet-code)

#### Configuration precedence and `puppet-code`

If no other configuration is specified, `puppet-code` uses the settings in the  global configuration file. User-specific configuration files override the global configuration file.

If you specify a configuration file on the command line, Puppet temporarily uses that configuration file **only** and does not read the global or user-specific config files at all. Finally, if you specify individual configuration options directly on the command line, those options temporarily take precedence over *any* configuration file settings.

## Deploying environments with `puppet-code`

To deploy environments with the `puppet-code` command, use the `deploy` action, either with the name of a single environment or with the `--all` flag. The `deploy` action deploys the environments, but returns only deployment *queuing* results by default. To view the results of the deployment itself, add the `--wait` flag. 

* To deploy a single environment, on the command line, run:

  ```puppet
puppet-code deploy mytestenvironment --wait
  ```

* To deploy all environments, on the command line, run:

  ```puppet
puppet-code deploy --all --wait
  ```
  
The `--wait` flag deploys the specified environments, waits for file sync to complete code deployment to the live code directory and all compile masters, and then returns results. In deployments that are geographically dispersed or have a large quantity of environments, completing code deployment can take up to several minutes.    

The resulting message includes the deployment signature, which is the commit SHA of the control repo used to deploy the environment. The output also includes two other SHAs that indicate that file sync is aware that the environment has been newly deployed to the code-staging directory. 

> #### Running `puppet-code` on Windows
>
>If you're running these commands from a managed or non-managed Windows workstation, you must specify the full path to the command. For example:
>
>`C:\Program Files\Puppet Labs\Client\bin\puppet code deploy mytestenvironment --wait`

### Deploying with a custom configuration file

You can specify a custom `puppet-code.conf` configuration file by using the `--config-file` option.

To deploy all environments using configuration settings in a specified config file, run:

```puppet
puppet-code --config-file ~/.puppetlabs/myconfigfile/puppet code.conf deploy --all
```

If the specified config file is missing any settings, `puppet-code` uses defaults, rather than falling back to the global or user-specific configurations.

### Deploying with command-line configuration settings

To temporarily override default, global, and user-specific configuration settings, specify the following configuration options on the command line:

* [`--cacert`](#cacert)
* [`--token-file`](#token-file), `-t`
* [`--service-url`](#service-url)

To override an existing configuration setting one time, specify that setting on the command line:

```puppet
puppet-code --service-url "https://puppet.example.com:8170/code-manager" deploy mytestenvironment
```

This example deploys 'mytestenvironment' using global or user-specific config settings (if set), except for `--service-url`, for which it uses the value specified on the command line ("https://puppet.example.com:8170/code-manager").

## Advanced `puppet-code` configuration

You can set up the `puppet-code` command on an agent node or on a workstation not managed by PE. And on any machine, you can set up user-specific config files.

The `puppet-code.conf` file is a JSON configuration file for the `puppet-code` command. For Unix-like or OS X operating systems, it looks something like:

```json
{
"cacert": "/etc/puppetlabs/puppet/ssl/certs/ca.pem",
"token-file": "~/.puppetlabs/token",
"service-url": "https://<PUPPET MASTER HOSTNAME>:8170/code-manager"
}
```

For Windows systems, use the entire Windows path, such as:

```json
{
"cacert": "C:\\ProgramData\\PuppetLabs\\puppet\\etc\\ssl\\certs\\ca.pem",
"token-file": "C:\\Users\\<username>\\.puppetlabs\\token",
"service-url": "https://<PUPPET MASTER HOSTNAME>:8170/code-manager"
}
```

### Configure `puppet-code` on agents and workstations

To use `puppet-code` on an agent node or on a workstation that is not managed by PE, install the client tools package and configure `puppet-code` on that machine.

Before you begin, download and install the client tools package. See the [client tools documentation][install_client] for instructions.

1. Create a config file called `puppet-code.conf` in the client tools directory.
   * For *nix or OS X systems, the default client tools directory is `/etc/puppetlabs/client-tools`.
   * For Windows systems, the default client tools directory is `C:\ProgramData\PuppetLabs\client-tools`.

1. In the `puppet-code.conf` file, specify your configuration settings in JSON format, as shown above.

### Configure `puppet-code` for different users

On any machine, whether it is a Puppet master, Puppet agent, or a workstation not managed by PE, you can set up specific `puppet-code` configurations for specific users.

Before you begin, if PE is **not** installed on the workstation you are configuring, see instructions for configuring `puppet-code` on agents and workstations.

1. Create a `puppet-code.conf` file in that user’s client tools directory.
   * For *nix or OS X systems, place the file in that user's `~/.puppetlabs/client-tools/`.
   * For Windows systems, place the file in the default user config file location, `%USERPROFILE%\.puppetlabs\client-tools\puppet-code.conf`.

2. In the user's `puppet-code.conf` file, specify the `cacert`, `token-file`, and `service-url` settings in JSON format, as shown above.


## Reference: `puppet-code` command

Usage: `puppet-code [global options] <action> [action arguments]`

### Global `puppet-code` options

Option   | Action   | Accepted arguments
----------------|:---------------:|:------------------:|-------------------------
`--help`, `-h` | Prints usage information for `puppet-code`. | none
`--version`, `-V` | Prints the application version. | none
`--log-level`, `-l` | Sets the log verbosity. It accepts one log level as an argument. | log levels: none, trace, debug, info, warn, error, fatal.
`--config-file`, `-c` | Sets a `puppet-code.conf` file that takes precedence over all other existing `puppet-code.conf` files. | A path to a `puppet-code.conf` file.
`--cacert` | Sets a Puppet CA certificate that overrides the `cacert` setting in any configuration files. | A path to the location of a CA Certificate.
`--token-file`, `-t` | Sets an authentication token that overrides the `token-file` setting in any configuration files. | A path to the location of the authentication token.
`--service-url` | Sets a base URL for the Code Manager service, overriding `service-url` settings in any configuration files. | A valid URL to the service.

### `puppet-code` actions

Action   | Result   | Arguments      | Options
----------------|:---------------:|:------------------:|-------------------------
`puppet-code print-config` | Prints out the resolved `puppet-code` configuration. | none    | none
`puppet-code deploy` | Runs remote code deployments with the Code Manager service. By default, returns only deployment queuing results. | Accepts either the name of a single environment or the `--all` flag.    | `--wait`, `-w`; `--all`; `--dry-run`; `--format`, `-F`
`puppet-code status` | Checks whether Code Manager and file sync are responding. By default, details are returned at the info level. | Accepts log levels none, trace, info, warn, error, fatal.    | none

Options for `puppet-code deploy` behave as follows:

* `--wait`, `-w`: The `--wait` flag causes `puppet-code deploy` to start a deployment, wait for the deployment to complete, wait for file sync to deploy the code to all compile masters, and then return the deployment signature with control repo commit SHAs for each environment. The output also includes two other SHAs that indicate that file sync is aware that the environment has been newly deployed to the code-staging directory.  
* `--all`: The `--all` flag causes `puppet-code deploy` to start deployments for all Code Manager environments.
* `--dry-run`: The `--dry-run` flag tests the connections to each configured remote and, if successfully connected, returns a consolidated list of the environments from all remotes. The `--dry-run` flag implies both `--all` and `--wait`. 
* `--format`, `-F`: Specifies the output format. Currently, the default and only supported value is 'json'.


### `puppet-code.conf` configuration settings

Setting   | Controls   | Unix-like and OS X default      | Windows default
----------------|:---------------:|:------------------:|-------------------------
`cacert` | Specifies the path to the Puppet CA certificate to use when connecting to the Code Manager service over SSL. | `/etc/puppetlabs/puppet/ssl/certs/ca.pem`    | `C:\ProgramData\PuppetLabs\puppet\etc\ssl\certs\ca.pem`
`token-file` | Specifies the location of the file containing the authentication token for Code Manager. | `~/.puppetlabs/token`    | `C:\Users\<username>\.puppetlabs\token`
`service-url` | Specifies the base URL of the Code Manager service. | `https://<PUPPET MASTER HOSTNAME>:8170/code-manager` | `https://<PUPPET MASTER HOSTNAME>:8170/code-manager`
