# Configuring Puppet orchestrator

Once you've installed PE or the client tools package, there are a few tasks you need to do to prepare your PE infrastructure for orchestration services.

-   Setting PE RBAC permissions and token authentication for Puppet orchestrator
-   Enable cached catalogs for use with the orchestrator \(optional\)
-   Review the orchestrator configuration files and adjust them as needed

All of these instructions assume that PE client tools are installed.

**Related topics**  


[Installing PE client tools](installing_pe_client_tools.md#)

## Setting PE RBAC permissions and token authentication for orchestrator

Before you run any orchestrator jobs, you need to set the appropriate permissions in PE's role-based access control \(RBAC\) and establish token-based authentication.

Most orchestrator users require the following permissions to run orchestrator jobs or tasks:

|Type|Permission|Definition|
|----|----------|----------|
|Puppet agent|Run Puppet on agent nodes.|The ability to run Puppet on nodes using the console or orchestrator. Instance must always be `"*"`.|
|Orchestrator|View orchestrator|The ability to view jobs. Instance must always be `"*"`. You must have this permission to run Puppet or tasks.|
|Tasks|Run a task on an agent node|The ability to run a task on an agent node. You can set this permission to `"*"` to grant access to run all tasks, or you can set it on a per-task basis.|
|Nodes|View node data from PuppetDB.|The ability to view node data imported from PuppetDB. Object must always be `"*"`.|

### Assign task permissions to a user role

1.  In the console, click **Access control** \> **User roles**.

2.  From the list of user roles, click the one you want to have task permissions.

3.  On the **Permissions** tab, in the **Type** box, select **Tasks**.

4.  For **Permission**, select **Run tasks**, and then select a task from the **Object** list. for example, **facter**.

5.  Click **Add permission**, and then commit the change.


### Using token authentication

Before running an orchestrator job, you must generate an RBAC access token to authenticate to the orchestration service. If you attempt to run a job without a token, PE will prompt you to supply credentials.

For information about generating a token with the CLI, see the documentation on token-based authentication.

**Related topics**  


[Create a new user role](rbac_user_roles_intro.md#)

**Related topics**  


[Assign permissions to a user role](rbac_user_roles_intro.md#)

## Enable cached catalogs for use with the orchestrator \(optional\)

Enabling cached catalogs on your agents ensures Puppet will not enforce any catalog changes on your agents until you run an orchestrator job to enforce changes.

### About this task

When you use the orchestrator to enforce change in a Puppet environment \(for example, in the `production` environment\), you want the agents in that environment to rely on their cached catalogs until you run an orchestrator job that includes configuration changes for those agents. Agents in such environments will check in during the run interval \(30 minutes by default\) to reinforce configuration in their cached catalogs and only apply new configuration when you run Puppet with an orchestration job.

**Note:** This is an optional configuration. You can run Puppet on nodes with the orchestrator in workflows that don't require cached catalogs.

### Procedure

1.  Run Puppet on the new agents.

    **Important:** Be sure you run Puppet on the new agents **before** assigning any application components to them or performing the next step.

2.  In each agent's `puppet.conf` file, in the `[agent]`section, add `use_cached_catalog=true`. To complete this step, choose one of the following methods:

    -   From the command line on each agent machine, run the following command:

        ```
         `puppet config set use_cached_catalog true --section agent`
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

If present, the user specified configuration always takes precedence over the global configuration file. For example, if both files have contradictory settings for the **environment**, the user specified settings will prevail.

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
|`color`|Determines whether the orchestrator output will use color. Set to `true` or `false`.|
|`noop`|Determines whether the orchestrator should run the Puppet agent in no-op mode. Set to `true` or `false`.|

## Disabling application management or orchestration services

Both application management and orchestration services are on by default in PE. If you need to disable these services, refer to Disabling application management and Disabling orchestration services.

