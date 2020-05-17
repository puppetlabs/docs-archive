# Configuring Puppet orchestrator

Once you've installed PE or the client tools package, there are a few tasks you need to do to prepare your PE infrastructure for orchestration services.

-   Set PE RBAC permissions and token authentication for Puppet orchestrator
-   Enable cached catalogs for use with the orchestrator \(optional\)
-   Review the orchestrator configuration files and adjust them as needed

All of these instructions assume that PE client tools are installed.

**Related information**  


[Installing PE client tools](installing_pe_client_tools.md#)

## Orchestration services settings

### `global.conf`: Global logging and SSL settings

`/etc/puppetlabs/orchestration-services/conf.d/global.conf` contains settings shared across the Puppet Enterprise \(PE\) orchestration services.

The file `global.certs` typically requires no changes and contains the following settings:

|Setting|Definition|Default|
|-------|----------|-------|
|`ssl-cert`|Certificate file path for the orchestrator host.|`/etc/puppetlabs/orchestration-services/ssl/<orchestrator-host-fqdn>.cert.pem`|
|`ssl-key`|Private key path for the orchestrator host.|`/etc/puppetlabs/orchestration-services/ssl/<orchestrator-host-fqdn>.private_key.pem`|
|`ssl-ca-cert`|CA file path|`/etc/puppetlabs/puppet/ssl/ca.pem`|

The file `global.logging-config` is a path to `logback.xml` file that configures logging for most of the orchestration services. See [http://logback.qos.ch/manual/configuration.html](http://logback.qos.ch/manual/configuration.html) for documentation on the structure of the `logback.xml` file. It configures the log location, rotation, and formatting for the following:

-   orchestration-services \(appender section `F1`\)
-   orchestration-services status \(`STATUS`\)
-   pcp-broker \(`PCP`\)
-   pcp-broker access \(`PCP_ACCESS`\)
-   aggregate-node-count \(`AGG_NODE_COUNT`\)

### `bootstrap.cfg`: Allow list of trapperkeeper services to start

`/etc/puppetlabs/orchestration-services/bootstrap.cfg` is the list of trapperkeeper services from the orchestrator and pcp-broker projects that are loaded when the `pe-orchestration-services` system service starts.

-   To disable a service in this list, remove it or comment it with a `#` character and restart `pe-orchestration-services`
-   To enable an NREPL service for debugging, add `puppetlabs.trapperkeeper.services.nrepl.nrepl-service/nrepl-service` to this list and restart `pe-orchestration-services`.

### `webserver.conf` and `web-routes.conf`: The pcp-broker and orchestrator HTTP services

`/etc/puppetlabs/orchestration-services/conf.d/webserver.conf` describes how and where to the run pcp-broker and orchestrator web services, which accept HTTP API requests from the rest of the PE installation and from external nodes and users.

The file `webserver.orchestrator` configures the orchestrator web service. Defaults are as follows:

|Setting|Definition|Default|
|-------|----------|-------|
|`access-log-config`|A logback XML file configuring logging for orchestrator access messages.|`/etc/puppetlabs/orchestration-services/request-logging.xml`|
|`client-auth`|Determines the mode that the server uses to validate the client's certificate for incoming SSL connections.|`want` or `need`|
|`default-server`|Allows multi-server configurations to run operations without specifying a server-id. Without a server-id, operations will run on the selected default. Optional.|`true`|
|`ssl-ca-cert`|Sets the path to the CA certificate PEM file used for client authentication.|`/etc/puppetlabs/puppet/ssl/certs/ca.pem`|
|`ssl-cert`|Sets the path to the server certificate PEM file used by the web service for HTTPS.|`/etc/puppetlabs/orchestration-services/ssl/<orchestrator-host-fqdn>.cert.pem`|
|`ssl-crl-path`|Describes a path to a Certificate Revocation List file. Optional.|`/etc/puppetlabs/puppet/ssl/crl.pem`|
|`ssl-host`|Sets the host name to listen on for encrypted HTTPS traffic.|`0.0.0.0.`|
|`ssl-key`|Sets the path to the private key PEM file that corresponds with the `ssl-cert`|`/etc/puppetlabs/orchestration-services/ssl/<orchestrator-host-fqdn>.private_key.pem`|
|`ssl-port`|Sets the port to use for encrypted HTTPS traffic.|`8143`|

The file `webserver.pcp-broker` configures the pcp-broker web service. Defaults are as follows:

|Setting|Definition|Default|
|-------|----------|-------|
|`client-auth`|Determines the mode that the server uses to validate the client's certificate for incoming SSL connections.|`want` or `need`|
|`ssl-ca-cert`|Sets the path to the CA certificate PEM file used for client authentication.|`/etc/puppetlabs/puppet/ssl/certs/ca.pem`|
|`ssl-cert`|Sets the path to the server certificate PEM file used by the web service for HTTPS.|`/etc/puppetlabs/orchestration-services/ssl/<orchestrator-host-fqdn>.cert.pem`|
|`ssl-crl-path`|Describes a path to a Certificate Revocation List file. Optional.|`/etc/puppetlabs/puppet/ssl/crl.pem`|
|`ssl-host`|Sets the host name to listen on for encrypted HTTPS traffic.|`0.0.0.0.`|
|`ssl-key`|Sets the path to the private key PEM file that corresponds with the `ssl-cert`.|`/etc/puppetlabs/orchestration-services/ssl/<orchestrator-host-fqdn>.private_key.pem`|
|`ssl-port`|Sets the port to use for encrypted HTTPS traffic.|`8142`|

`/etc/puppetlabs/orchestration-services/conf.d/web-routes.conf` describes how to route HTTP requests made to the API web servers, designating routes for interactions with other services. These should not be modified. See the configuration options at the [trapperkeeper-webserver-jetty project's docs](https://github.com/puppetlabs/trapperkeeper-webserver-jetty9/blob/master/doc/webrouting-config.md)

### `analytics.conf`: Analytics trapperkeeper service configuration

`/etc/puppetlabs/orchestration-services/conf.d/analytics.conf` contains the internal setting for the [analytics](https://github.com/puppetlabs/analytics) trapperkeeper service.

|Setting|Definition|Default|
|-------|----------|-------|
|`analytics.url`|Specifies the API root.|`<puppetserver-host-url>:8140/analytics/v1`|

### `auth.conf`: Authorization trapperkeeper service configuration

`/etc/puppetlabs/orchestration-services/conf.d/auth.conf` contains internal settings for the authorization trapperkeeper service. See configuration options in the [trapperkeeper-authorization project's docs](https://github.com/puppetlabs/trapperkeeper-authorization/blob/master/doc/authorization-config.md).

### `metrics.conf`: JXM metrics trapperkeeper service configuration

`/etc/puppetlabs/orchestration-services/conf.d/metrics.conf` contains internal settings for the JMX metrics service built into orchestration-services. See the service configuration options in the [trapperkeeper-metrics project's docs](https://github.com/puppetlabs/trapperkeeper-metrics/blob/master/documentation/configuration.md).

### `orchestrator.conf`: Orchestrator trapperkeeper service configuration

`/etc/puppetlabs/orchestration-services/conf.d/orchestrator.conf` contains internal settings for the orchestrator project's trapperkeeper service.

### `pcp-broker.conf`: PCP broker trapperkeeper service configuration

`/etc/puppetlabs/orchestration-services/conf.d/pcp-broker.conf` contains internal settings for the pcp-broker project's trapperkeeper service. See the service configuration options in the [pcp-broker project's docs](https://github.com/puppetlabs/pcp-broker/blob/master/doc/configuration.md#service-configuration).

## Setting PE RBAC permissions and token authentication for orchestrator

Before you run any orchestrator jobs, you need to set the appropriate permissions in PE role-based access control \(RBAC\) and establish token-based authentication.

Most orchestrator users require the following permissions to run orchestrator jobs or tasks:

|Type|Permission|Definition|
|----|----------|----------|
|Puppet agent|Run Puppet on agent nodes.|The ability to run Puppet on nodes using the console or orchestrator. Instance must always be `"*"`.|
|Job orchestrator|Start, stop and view jobs|The ability to start and stop jobs and tasks, view jobs and job progress, and view an inventory of nodes that are connected to the PCP broker.|
|Tasks|Run tasks|The ability to run specific tasks on all nodes, a selected node group, or nodes that match a PQL query.|
|Nodes|View node data from PuppetDB.|The ability to view node data imported from PuppetDB. Object must always be `"*"`.|

**Note:** If you do not have permissions to view a node group, or the node group doesn't have any matching nodes, that node group won't be listed as an option for viewing. In addition, a node group does not appear if no rules have been specified for it.

### Assign task permissions to a user role

1.  In the console, click **Access control** \> **User roles**.

2.  From the list of user roles, click the one you want to have task permissions.

3.  On the **Permissions** tab, in the **Type** box, select **Tasks**.

4.  For **Permission**, select **Run tasks**, and then select a task from the **Object** list. For example, **facter\_task**.

5.  Click **Add permission**, and then commit the change.


### Using token authentication

Before running an orchestrator job, you must generate an RBAC access token to authenticate to the orchestration service. If you attempt to run a job without a token, PE prompts you to supply credentials.

For information about generating a token with the CLI, see the documentation on token-based authentication.

**Related information**  


[Token-based authentication](rbac_token_auth_intro.md#)

[Create a new user role](rbac_user_roles_intro.md#)

[Assign permissions to a user role](rbac_user_roles_intro.md#)

## Enable cached catalogs for use with the orchestrator \(optional\)

Enabling cached catalogs on your agents ensures Puppet does not enforce any catalog changes on your agents until you run an orchestrator job to enforce changes.

### About this task

When you use the orchestrator to enforce change in a Puppet environment \(for example, in the `production` environment\), you want the agents in that environment to rely on their cached catalogs until you run an orchestrator job that includes configuration changes for those agents. Agents in such environments check in during the run interval \(30 minutes by default\) to reinforce configuration in their cached catalogs, and apply new configuration only when you run Puppet with an orchestration job.

**Note:** This is an optional configuration. You can run Puppet on nodes with the orchestrator in workflows that don't require cached catalogs.

### Procedure

1.  Run Puppet on the new agents.

    **Important:** Be sure you run Puppet on the new agents **before** assigning any application components to them or performing the next step.

2.  In each agent's `puppet.conf` file, in the `[agent]`section, add `use_cached_catalog=true`. To complete this step, choose one of the following methods:

    -   From the command line on each agent machine, run the following command:

        ```
        puppet config set use_cached_catalog true --section agent
        ```

    -   Add an `ini_setting` resource in the `node default {}` section of the environment’s `site.pp`. This adds the setting to **all** agents in that environment.  

        ```
        if $facts['kernel'] = 'windows' {
          $config = 'C:/ProgramData/PuppetLabs/puppet/etc/puppet.conf'
        } else {
          $config = $settings::config
        }
         
        ini_setting { 'use_cached_catalog':
          ensure  => present,
          path    => $config,
          section => 'agent',
          setting => 'use_cached_catalog',
          value   => 'true',
        }
        ```

3.  Run Puppet on the agents again to enforce this configuration.


## Orchestrator configuration files

The configuration file for the orchestrator allows you to run commands from the CLI without having to pass additional flags. Whether you are running the orchestrator from the Puppet master or from a separate work station, there are two types of configuration files: a global configuration file and a user-specified configuration file.

### Orchestrator global configuration file

If you're running the orchestrator from a PE-managed machine, on either the Puppet master or an agent node, PE manages the global configuration file.

This file is installed on both managed and non-managed workstations at:

-   **\*nix systems** --- `/etc/puppetlabs/client-tools/orchestrator.conf`
-   **Windows** --- `C:/ProgramData/PuppetLabs/client-tools/orchestrator.conf`

The class that manages the global configuration file is `puppet_enterprise::profile::controller`. The following parameters and values are available for this class:

|Parameter|Value|
|---------|-----|
|`manage_orchestrator`|`true` or `false` \(default is `true`\)|
|`orchestrator_url`|url and port \(default is Puppet master url and port 8143\)|

The only value PE sets in the global configuration file is the `orchestrator_url` \(which sets the orchestrator's `service-url` in `/etc/puppetlabs/client-tools/orchestrator.conf`\).

**Important:** If you're using a managed workstation, do not edit or change the global configuration file. If you're using an unmanaged workstation, you can edit this file as needed.

### Orchestrator user-specified configuration file

You can manually create a user-specified configuration file and populate it with orchestrator configuration file settings. PE does not manage this file.

This file needs to be located at `~/.puppetlabs/client-tools/orchestrator.conf` for both \*nix and Windows.

If present, the user specified configuration always takes precedence over the global configuration file. For example, if both files have contradictory settings for the **environment**, the user specified settings prevail.

### Orchestrator configuration file settings

The orchestrator configuration file is formatted in JSON. For example:

```JSON
{
  "options" : {
    "service-url": "https://<PUPPET MASTER HOSTNAME>:8143",
    "cacert": "/etc/puppetlabs/puppet/ssl/certs/ca.pem",
    "token-file": "~/.puppetlabs/token",
    "color": true
  }
}
```

The orchestrator configuration files \(the user-specified or global files\) can take the following settings:

|Setting|Definition|
|-------|----------|
|`service-url`|The URL that points to the Puppet master and the port used to communicate with the orchestration service. \(You can set this with the `orchestrator_url` parameter in the `puppet_enterprise::profile::controller`class.\) Default value: `https://<PUPPET MASTER HOSTNAME>:8143`|
|`environment`|The environment used when you issue commands with Puppet orchestrator.|
|`cacert`|The path for the Puppet Enterprise CA cert. -   \*nix: `/etc/puppetlabs/puppet/ssl/certs/ca.pem`

-   Windows: C:\\ProgramData\\PuppetLabs\\puppet\\etc\\ssl\\certs\\ca.pem


|
|`token-file`|The location for the authentication token. Default value: `~/.puppetlabs/token`|
|`color`|Determines whether the orchestrator output uses color. Set to `true` or `false`.|
|`noop`|Determines whether the orchestrator runs the Puppet agent in no-op mode. Set to `true` or `false`.|

## PE Bolt services configuration

The PE Bolt server is a Ruby service that enables SSH and WinRM tasks from the console. The server accepts requests from the orchestrator, calls out to Bolt to run the task, and returns the result.

### PE Bolt server configuration

The PE Bolt server provides an API for running tasks over SSH and WinRM using Bolt, which is the technology underlying PE tasks. The API server for tasks is available as pe-bolt-server.

The server is a [Puma](https://puma.io) application that runs as a standalone service.

The server is configured in `/etc/puppetlabs/bolt-server/conf.d/bolt-server.conf`, managed by the `puppet_enterprise::profile::bolt_server` class, which includes these parameters:

|Setting|Type|Description|Default|
|-------|----|-----------|-------|
|`bolt_server_loglevel`|string|Bolt log level. Acceptable values are `debug`, `info`, `notice`, `warn`, and `error`.|`notice`|
|`concurrency`|integer|Maximum number of server threads.|100|
|`master_host`|string|URI of the master where Bolt can download tasks.|`$puppet_enterprise::puppet_master_host`|
|`master_port`|integer|Port the Bolt server can access the master on.|`$puppet_enterprise::puppet_master_port`|
|`ssl_cipher_suites`|array\[string\]|TLS cipher suites in order of preference.|`$puppet_enterprise::params::secure_ciphers`|
|`ssl_listen_port`|integer|Port the Bolt server runs on.|62658 \(`$puppet_enterprise::bolt_server_port`\)|
|`whitelist`|array\[string\]|List of hosts that can connect to pe-bolt-server.|`[$certname]`|

## PE ACE services configuration

The PE Agentless Catalog Executor server is a Ruby service that enables you to execute Bolt Tasks and Puppet catalogs remotely.

### PE ACE server configuration

The PE ACE server provides an API for running tasks and catalogs against remote targets.

The server is a [Puma](https://puma.io) application that runs as a standalone service.

The server is configured in `/etc/puppetlabs/ace-server/conf.d/ace-server.conf, managed by the puppet_enterprise::profile::ace_server` class, which includes these parameters:

|Setting|Type|**Description**|Default|
|-------|----|---------------|-------|
|`service_loglevel`|string|Bolt log level. Acceptable values are `debug`, `info`, `notice`, `warn`, and `error`.|`notice`|
|`concurrency`|integer|Maximum number of server threads.|`$puppet_enterprise::ace_server_concurrency`|
|`master_host`|string|URI that ACE can access the master on.|`pe_repo::compile_master_pool_address`

 `default: $puppet_enterprise::puppet_master_host`

|
|`master_port`|integer|Port that ACE can access the master on.|`$puppet_enterprise::puppet_master_port`

|
|`hostcrl`|string|The host CRL path|`$puppet_enterprise::params::hostcrl`

|
|`ssl_cipher_suites`|array\[string\]|TLS cipher suites in order of preference.|`$puppet_enterprise::params::secure_ciphers`

|
|`ssl_listen_port`|integer|Port that ACE runs on.|44633

 `($puppet_enterprise::ace_server_port)`

|
|`whitelist`|array\[string\]|List of hosts that can connect to pe-ace-server.|`[$certname]`

|

## Disabling application management or orchestration services

Both application management and orchestration services are on by default in PE. If you need to disable these services, refer to Disabling application management and Disabling orchestration services.

