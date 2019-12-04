---
layout: default
title: "Configuring Puppet orchestrator"
canonical: "/pe/latest/orchestrator_install.html"
---

The Puppet orchestrator is part of the [pe-client-tools package](./install_pe_client_tools.html). It is automatically installed on the same node as the Puppet master, or you can install it on a separate workstation that may or may not be managed by Puppet Enterprise.

Once you've installed PE or the client tools package, there are a few tasks you need to do to prepare your PE infrastructure for orchestration services.

1. [Setting PE RBAC permissions and token authentication for Puppet orchestrator](#setting-pe-rbac-permissions-and-token-authentication-for-puppet-orchestrator)
2. [Enable cached catalogs for use with the orchestrator (optional)](#enable-cached-catalogs-for-use-with-the-orchestrator-optional)
3. Review [Puppet orchestrator configuration files](#puppet-orchestrator-configuration-files) and adjust them as needed

Before you begin: If you have not already installed the PE client tools package, follow the steps in [Installing the PE client tools package](./install_pe_client_tools.html).

## Setting PE RBAC permissions and token authentication for Puppet orchestrator

Before you run any orchestrator jobs, you need to set the appropriate permissions in PE's role-based access control (RBAC) and establish token-based authentication.

Most users require the following permissions:

Type              | Permission                   | Definition
------------------|------------------------------|------------
Orchestration      | Run Puppet from orchestrator | The ability to run Puppet on nodes using the orchestrator. Instance must always be `"*"`. 
Nodes             | View node data from PuppetDB | The ability to view node data imported from PuppetDB. Object must always be `"*"`.

The following links provide instructions for creating user roles and setting permissions:

1. [Create a new user role](./rbac_user_roles.html#create-a-new-user-role)
2. [Assign permissions to a user role](./rbac_user_roles.html#assign-permissions-to-a-user-role)
3. [Add a user to a role](./rbac_user_roles.html#add-a-user-to-a-user-role)

The RBAC permissions page has a full list of [available permissions](./rbac_permissions.html#available-permissions).

### Using token authentication

Before running an orchestrator job, you must generate an RBAC access token to authenticate to the orchestration service. If you attempt to run a job without a token, PE will prompt you to supply credentials.

For information about generating a token with the CLI, see the documentation on [token-based authentication](./rbac_token_auth.html#generating-a-token-using-puppet-access).

## Enable cached catalogs for use with the orchestrator (optional)

Enabling cached catalogs on your agents ensures Puppet will not enforce any catalog changes on your agents until you run an orchestrator job to enforce changes.  

When you use the orchestrator to enforce change in a Puppet environment (for example, in the `production` environment), you want the Puppet agents in that environment to rely on their cached catalogs until you run an orchestrator job that includes configuration changes for those agents. Puppet agents in such environments will check in during the runinterval (30 minutes by default) to reinforce configuration in their cached catalogs and only apply new configuration when you run Puppet with an orchestration job.

>**Note:** This is an optional configuration. You can run Puppet on nodes with the orchestrator in workflows that don't require cached catalogs.

1. If you’ve not already done so, [install your Puppet agents](./install_agents.html) and [sign the agent cert](./console_cert_mgmt.html#rejecting-and-approving-nodes).
2. Run Puppet on the new agents.

   Be sure you run Puppet on the new agents **before** assigning any application components to them or performing the next step.

3. In each agent’s `puppet.conf` file, add the following settings to the `[agent]` section:

   ~~~
   use_cached_catalog=true
   ~~~

   If you have many agents participating in orchestration services, manually editing each agent's `puppet.conf` file to add these settings can be cumbersome. Instead, add the settings using one of the following methods:

   - From the command line on each agent machine, run:

     ~~~
     puppet config set use_cached_catalog true --section agent
     ~~~

   - Add an `ini_setting` resource in the `node default {}` section of the environment’s `site.pp`. 

     ~~~    
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
     ~~~

   Note that this last example adds this setting to **all** agents in that environment.

4. Run Puppet on the agents again to enforce this configuration.


>**Note:** Refer to the [static catalog documentation](./static_catalogs.html) for an overview on how static catalogs prevent file resource errors when running cached catalogs.

## Puppet orchestrator configuration files

The configuration file for the Puppet orchestrator allows you to run commands from the CLI without having to pass additional flags.

Whether you are running the orchestrator from the Puppet master or from a separate work station, there are two types of configuration files: a global configuration file and a user-specified configuration file.

### Orchestrator global configuration file 

If you're running the Puppet orchestrator from a PE-managed machine, on either the Puppet master or a [Puppet agent node](./install_pe_client_tools.html#pe-managed-workstation-installation), PE manages the global configuration file. 

This file is installed on both managed and non-managed workstations at:

   * ***nix systems** --- `/etc/puppetlabs/client-tools/orchestrator.conf`
   * **Windows** --- `C:/ProgramData/PuppetLabs/client-tools/orchestrator.conf`

The class that manages the global configuration file is `puppet_enterprise::profile::controller`. The following parameters and values are available for this class:

| Parameter | Value |
|----------|--------- |
| `manage_orchestrator` | `true` or `false` (default is `true`) |
| `orchestrator_url`  |  url and port  (default is Puppet master url and port 8143)|

The only value PE sets in the global configuration file is the `orchestrator_url` (which sets the orchestrator's `service-url` in `/etc/puppetlabs/client-tools/orchestrator.conf`).

>**Important:** If you're using a managed workstation, do not edit or change the global configuration file. If you're using an unmanaged workstation, you can edit this file as needed.  

### Orchestrator user-specified configuration file

   You can manually create a user-specified configuration file and populate it with orchestrator configuration file settings. PE does not manage this file.
 
  This file needs to be located at `~/.puppetlabs/client-tools/orchestrator.conf` for both *nix and Windows. 
    
  If present, the user specified configuration always takes precedence over the global configuration file. For example, if both files have contradictory settings for the **environment**, the user specified settings will prevail.

### Orchestrator configuration file settings

The orchestrator configuration file is formatted in JSON. For example:

~~~JSON
{
  "options" : {
    "service-url": "https://<PUPPET MASTER HOSTNAME>:8143",
    "environment": "production",
    "cacert": "/etc/puppetlabs/puppet/ssl/certs/ca.pem",
    "token-file": "~/.puppetlabs/token",
    "color": true,
    "noop": false
  }
}
~~~

The orchestrator configuration files (the user-specified or global files) can take the following settings:

<table>
 <tr>
   <th>Setting</th>
   <th>Definition</th>
 </tr>
 <tr>
   <td><code>service-url</code></td>
   <td>The URL that points to the Puppet master and the port used to communicate with the orchestration service. (You can set this with the <code>orchestrator_url</code> parameter in the <code>puppet_enterprise::profile::controller</code> class.) <br>Default value: <code>https://{PUPPET MASTER HOSTNAME}:8143</code></td>
 </tr> 
 <tr>
   <td><code>environment</code></td>
   <td>The environment used when you issue commands with Puppet orchestrator. This will default to the <code>production</code>environment if you don't set it to another environment.</td>
 </tr> 
 <tr>
   <td><code>cacert</code></td>
   <td>The path for the Puppet Enterprise CA cert. <p> <ul><li>*nix --- <code>/etc/puppetlabs/puppet/ssl/certs/ca.pem</code></li><li>Windows --- <code>C:\ProgramData\PuppetLabs\puppet\etc\ssl\certs\ca.pem</code></li></ul></p></td>
 </tr> 
 <tr>
   <td><code>token-file</code></td>
   <td>The location for the authentication token.<br> Default value: <code>~/.puppetlabs/token</code></td>
 </tr> 
 <tr>
   <td><code>color</code></td>
   <td>Determines whether the orchestrator output will use color. Set to <code>true</code> or <code>false</code>.</td>
 </tr> 
 <tr>
   <td><code>noop</code></td>
   <td>Determines whether the orchestrator should run the Puppet agent in no-op mode. Set to <code>true</code> or <code>false</code>.</td>
 </tr>
</table>     

## Disabling application management or orchestration services

Both application management and orchestration services are on by default in PE. If you need to disable these services, refer to [Disabling application management](./config_orchestration.html#disabling-application-management) and [Disabling orchestration services](./config_orchestration.html#disabling-orchestration-services).
