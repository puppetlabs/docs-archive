# Triggering Code Manager on the command line

The `puppet-code` command allows you to trigger Code Manager from the command line to deploy your environments.

## Installing and configuring puppet-code

PE automatically installs and configures the `puppet-code` command on your masters as part of the included PE client tools package. You can also set up `puppet-code` on an agent node or on a workstation, customize configuration for different users, or change the configuration settings.

The global configuration settings for Linux and macOS systems are in a JSON file at `/etc/puppetlabs/client-tools/puppet-code.conf`. The default configuration file looks something like:

```json
{
"cacert": "/etc/puppetlabs/puppet/ssl/certs/ca.pem",
"token-file": "~/.puppetlabs/token",
"service-url": "https://<PUPPET MASTER HOSTNAME>:8170/code-manager"
 }
```

**Important:**

On a PE-managed machine, Puppet manages this file for you. Do not manually edit this file, because Puppet will overwrite your new values the next time it runs.

Additionally, you can set up `puppet-code` on an agent node or on a workstation not managed by PE. And on any machine, you can set up user-specific config files. For instructions, see the related topic about advanced configuration of `puppet-code`.

You can also override existing configuration settings on a case-by-case basis on the command line. When you deploy environments with the `puppet-code deploy` command, you can specify either an alternative config file or particular config settings directly in the command. For examples, see the related topic about deploying environments with `puppet code`.

### Windows paths

The global `puppet-code` configuration file on Windows systems is located at `C:\ProgramData\PuppetLabs\client-tools\puppet-code.conf`.

The default configuration file looks something like:

```json
{
"cacert": "C:\\ProgramData\\PuppetLabs\\puppet\\etc\\ssl\\certs\\ca.pem",
"token-file": "C:\\Users\\<username>\\.puppetlabs\\token",
"service-url": "https://<PUPPET MASTER HOSTNAME>:8170/code-manager"
 }
```

### Configuration precedence and puppet-code

There are several ways to configure `puppet-code`, but some configuration methods take precedence over others.

If no other configuration is specified, `puppet-code` uses the settings in the global configuration file. User-specific configuration files override the global configuration file.

If you specify a configuration file on the command line, Puppet temporarily uses that configuration file **only** and does not read the global or user-specific config files at all.

Finally, if you specify individual configuration options directly on the command line, those options temporarily take precedence over *any* configuration file settings.

## Deploying environments with puppet-code

To deploy environments with the `puppet-code` command, use the `deploy` action, either with the name of a single environment or with the `--all` flag.

The `deploy` action deploys the environments, but returns only deployment *queuing* results by default. To view the results of the deployment itself, add the `--wait` flag.

The `--wait` flag deploys the specified environments, waits for file sync to complete code deployment to the live code directory and all compile masters, and then returns results. In deployments that are geographically dispersed or have a large quantity of environments, completing code deployment can take up to several minutes.

The resulting message includes the deployment signature, which is the commit SHA of the control repo used to deploy the environment. The output also includes two other SHAs that indicate that file sync is aware that the environment has been newly deployed to the code staging directory.

To temporarily override default, global, and user-specific configuration settings, specify the following configuration options on the command line:

-   `--cacert`
-   `--token-file`, `-t`
-   `--service-url`

Alternately, you can specify a custom `puppet-code.conf` configuration file by using the `--config-file` option.

### Running puppet-code on Windows

If you're running these commands from a managed or non-managed Windows workstation, you must specify the full path to the command.

```

                `C:\Program Files\Puppet Labs\Client\bin\puppet code deploy mytestenvironment --wait`
            
```

### Deploy environments on the command line

To deploy environments, use the `puppet-code deploy` command, specifying the environments to deploy.

#### Procedure

1.  To deploy environments, on the command line, run `puppet-code deploy`, specifying the environment.

    Specify the environment by name. To deploy all environments, use the `--all` flag.

    Optionally, you can specify the `--wait` flag to return results after the deployment is finished. Without the `--wait` flag, the command returns only queuing results.

    `puppet-code deploy myenvironment --wait`

    `puppet-code deploy --all --wait`

    Both of these commands deploy the specified environments, and then return deployment results with a control repo commit SHA for each environment.


#### Results

**Related topics**  


[Reference: puppet-code command](puppet_code.md#)

### Deploy with a custom configuration file

You can deploy environments with a custom configuration file that you specify on the command line.

#### Procedure

1.  To deploy all environments using the configuration settings in a specified config file, run the command `puppet-code deploy` command with a `--config-file` flag specifying the location of the config file.

    `puppet-code --config-file ~/.puppetlabs/myconfigfile/puppet code.conf deploy --all`


### Deploy with command-line configuration settings

You can override an existing configuration setting on a per-use basis by specifying that setting on the command line.

#### About this task

#### Procedure

1.  Specify the setting, which will be used on this deployment only, on the command line.

    `puppet-code --service-url "https://puppet.example.com:8170/code-manager" deploy mytestenvironment`

    This example deploys 'mytestenvironment' using global or user-specific config settings \(if set\), except for `--service-url`, for which it uses the value specified on the command line \("https://puppet.example.com:8170/code-manager"\).


## Advanced puppet-code configuration

You can set up the `puppet-code` command on an agent node or on a workstation not managed by PE. And on any machine, you can set up user-specific config files.

You can set up the `puppet-code` command on an agent node or on a workstation not managed by PE. And on any machine, you can set up user-specific config files.

The `puppet-code.conf` file is a JSON configuration file for the `puppet-code` command. For Linux or Mac OS X operating systems, it looks something like:

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

**Related topics**  


[Installing PE client tools](installing_pe_client_tools.md#)

### Configure puppet-code on agents and workstations

To use `puppet-code` on an agent node or on a workstation that is not managed by PE, install the client tools package and configure `puppet-code` on that machine.

#### Before you begin

Download and install the client tools package. See the client tools documentation for instructions.

#### Procedure

1.  Create a config file called `puppet-code.conf` in the client tools directory.

    -   For Linux and Mac OS X systems, the default client tools directory is `/etc/puppetlabs/client-tools`
    -   For Windows systems, the default client tools directory is` C:\ProgramData\PuppetLabs\client-tools`

### Configure puppet-code for different users

On any machine, whether it is a master, an agent, or a workstation not managed by PE, you can set up specific `puppet-code` configurations for specific users.

#### Before you begin

If PE is **not** installed on the workstation you are configuring, see instructions for configuring `puppet-code` on agents and workstations.

#### About this task

#### Procedure

1.  Create a `puppet-code.conf` file in the user's client tools directory.

    -   For Linux or Mac OS X systems, place the file in the user's `~/.puppetlabs/client-tools/`

    -   For Windows systems, place the file in the default user config file location: `C:\Users\<username>\.puppetlabs\ssl\certs\ca.pem`

2.  In the user's `puppet-code.conf` file, specify the `cacert`, `token-file`, and `service-url` settings in JSON format. 


## Reference: puppet-code command

The `puppet-code` command accepts options, actions, and `deploy` action options.

Usage: `puppet-code [global options] <action> [action options]`

### Global puppet-code options

Global options set the behavior of the `puppet-code` command on the command line.

|Option|Description|Accepted arguments|
|------|:---------:|------------------|
|`--help`, `-h`|Prints usage information for `puppet-code`.|none|
|`--version`, `-V`|Prints the application version.|none|
|`--log-level`, `-l`|Sets the log verbosity. It accepts one log level as an argument.|log levels: none, trace, debug, info, warn, error, fatal.|
|`--config-file`, `-c`|Sets a `puppet-code.conf` file that takes precedence over all other existing `puppet-code.conf` files.|A path to a `puppet-code.conf` file.|
|`--cacert`|Sets a Puppet CA certificate that overrides the `cacert` setting in any configuration files.|A path to the location of a CA Certificate.|
|`--token-file`, `-t`|Sets an authentication token that overrides the `token-file` setting in any configuration files.|A path to the location of the authentication token.|
|`--service-url`|Sets a base URL for the Code Manager service, overriding `service-url` settings in any configuration files.|A valid URL to the service.|

### puppet-code actions

The `puppet-code` command can perform print, deploy, and status actions.

|Action|Result|Arguments|Options|
|------|:----:|:-------:|-------|
|`puppet-code print-config`|Prints out the resolved `puppet-code` configuration.|none|none|
|`puppet-code deploy`|Runs remote code deployments with the Code Manager service. By default, returns only deployment queuing results.|Accepts either the name of a single environment or the `--all` flag.|`--wait`, `-w`; `--all`; `--dry-run`; `--format`, `-F`|
|`puppet-code status`|Checks whether Code Manager and file sync are responding. By default, details are returned at the info level.|Accepts log levels none, trace, info, warn, error, fatal.|none|

#### puppet-code deploy action options

Modify the `puppet-code deploy` action with action options.

|Option|Description|
|------|-----------|
|`--wait`, `-w`|Causes `puppet-code deploy` to: 1.  Start a deployment,

2.  Wait for the deployment to complete,

3.  Wait for file sync to deploy the code to all compile masters, and

4.  Return the deployment signature with control repo commit SHAs for each environment.


The return output also includes two other SHAs that indicate that file sync is aware that the environment has been newly deployed to the code-staging directory.|
|`--all`|Tells `puppet-code deploy` to start deployments for all Code Manager environments.|
|`--dry-run`|Tests the connections to each configured remote and, if successfully connected, returns a consolidated list of the environments from all remotes. The `--dry-run` flag implies both `--all` and `--wait`.|
|`--format`, `-F`|Specifies the output format. The default and only supported value is 'json'.|

### puppet-code.conf configuration settings

Temporarily specify `puppet-code.conf` configuration settings on the command line.

|Setting|Controls|Linux and Mac OS X default|Windows default|
|-------|:------:|:------------------------:|---------------|
|`cacert`|Specifies the path to the Puppet CA certificate to use when connecting to the Code Manager service over SSL.|`/etc/puppetlabs/puppet/ssl/certs/ca.pem`|`C:\ProgramData\PuppetLabs\puppet\etc\ssl\certs\ca.pem`|
|`token-file`|Specifies the location of the file containing the authentication token for Code Manager.|`~/.puppetlabs/token`|`C:\Users\<username>\.puppetlabs\token`|
|`service-url`|Specifies the base URL of the Code Manager service.|`https://<PUPPET MASTER HOSTNAME>:8170/code-manager`|`https://<PUPPET MASTER HOSTNAME>:8170/code-manager`|

