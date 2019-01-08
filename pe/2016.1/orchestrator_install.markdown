---
layout: default
title: "Installing and configuring Puppet orchestrator"
canonical: "/pe/latest/orchestrator_install.html"
---


Puppet orchestrator is bundled in the [pe-client-tools package](./install_pe_client_tools.html). The pe-client-tools package is included in the PE installation tarball and, when you install PE, it is automatically installed on the same node as the Puppet master. However, you can install the pe-client-tools, including the Puppet orchestrator, on a separate workstation that isn’t managed by Puppet Enterprise.

When you run the orchestrator from a separate workstation, you don't need to SSH into the Puppet master to execute commands. Your permissions to take action and see information is controlled by PE role-based access control (RBAC) and your activity is logged under your username rather than as `root` or the `pe-puppet` user.

Follow these steps, detailed below, to install and configure Puppet orchestrator:

1. [Install the PE client tools package](#install-the-pe-client-tools), which includes the orchestrator client.
2. [Enable the Orchestration service](#enabling-the-orchestration-service).
3. [Configure the Puppet orchestrator client](#configure-the-puppet-orchestrator-client)
4. [Set PE RBAC permissions and token authentication for Puppet orchestrator](#set-pe-rbac-permissions-and-token-authentication-for-puppet-orchestrator)
5. [Prepare your agents for orchestration services](#prepare-your-agents-for-orchestration-services)

## Install the PE client tools

If you have not already installed the PE client tools package, follow the steps in [Installing the PE client tools package](./install_pe_client_tools.html).

## Enable the Application Orchestration Service and orchestrator client

The Puppet orchestrator is the CLI to the Application Orchestration service. To enable the orchestrator, you must first enable the Application Orchestration service.

**To enable the Application Orchestration service**:

>**Note**: If you enabled Application Orchestration services when you installed PE, you do not need to enable it a second time.

1. **In the PE console**, Click **Nodes** > **Classification**.
2. Select the **PE Infrastructure** group.
3. Click the **Classes** tab.
4. Locate the **puppet_enterprise** class and in the **parameter name** drop-down list select **use_application_services**.
5. In the **Value** field, change the value from `false` to `true`.
6. Click **Add parameter**, and then click the **Commit change** button.
7. **On the Puppet master**, run Puppet.

## Configure the Puppet orchestrator client

The configuration file for the Orchestration client allows you to run commands from the CLI without having to pass additional flags.

Whether you are running the orchestrator client from the Puppet master or installing it on a separate work station, there are two places where you can have a configuration file:

- A **global configuration file** at `/etc/puppetlabs/client-tools/orchestrator.conf`.

   This global configuration file is created by Puppet Enterprise for PE-managed machines only. PE will add the **service-url** setting.

   If you're running the client from a workstation, you will need to create the global file and populate it with the [configuration file settings](#orchestrator-client-configuration-file-settings).

- A **user-specified configuration file** at `~/.puppetlabs/client-tools/orchestrator.conf`, which you will need to create and populate with the config file settings.

>**Note**: These are new locations for the orchestrator client configurations. If you're attempting to run the client with a config file in the old location (`~/.puppetlabs/etc/puppet/orchestrator.conf`), you will receive a warning.

The user specified configuration always takes precedence over the global configuration file. For example, if both files have contradictory settings for the **environment**, the user specified settings will prevail.

Note that in either case, the configuration file for the orchestrator client must be named `orchestrator.conf`, and must be stored in the correct directory.

The config file is formatted in JSON. For example:

~~~JSON
{
  "options" : {
    "service-url": "https://master.example.com:8143",
    "environment": "production"
  }
}
~~~

### Puppet orchestrator global configuration file management

If you're running the Puppet orchestrator from a PE-managed machine, on either the Puppet master or a [Puppet agent node](./install_pe_client_tools.html#pe-managed-workstation-installation), you can have PE manage the global configuration file (`/etc/puppetlabs/client-tools/orchestrator.conf`). 

The class that manages the global configuration file is `puppet_enterprise::profile::controller`.

For the orchestrator, the following parameters and values are available for this class:

| Parameter | Value |
|----------|--------- |
| `manage_orchestrator` | `true` or `false` (default is `true`) |
| `orchestrator_url`  |  url and port  (default is Puppet master url and port 8143)|

The only value PE sets in the global configuration file is the `orchestrator_url` (which sets the orchestrator's `service-url` in `/etc/puppetlabs/client-tools/orchestrator.conf`).

You can add additional configuration settings manually.  

### Orchestrator client configuration file settings

The orchestrator configuration files (the user-specified or global files) can take the following settings:

- **service-url**: `https://{PUPPET MASTER HOSTNAME}:8143`---the URL that points to the Puppet master and the port used to communicate with the orchestration service. (You can set this with the `orchestrator_url` parameter in the `puppet_enterprise::profile::controller` class.) 
- **environment**: `{ENVIRONMENT NAME}`---the environment used when you issue commands with Puppet orchestrator. This will default to the `production` environment if you don't set it to another environment.
- **cacert**: `/etc/puppetlabs/puppet/ssl/certs/ca.pem`---the path for the Puppet Enterprise CA cert.
- **color**: (set to `true` or `false`)---determines whether the orchestrator output will use color.
- **verbose**: (set to `true` or `false`)---determines whether the orchestrator output will be verbose.


## Set PE RBAC permissions and token authentication for Puppet orchestrator

### Setting RBAC permissions

Before running any orchestrator jobs, make sure the correct role and permissions are set for any user who will run Orchestration jobs. The following links provide instructions:

1. [Create a new user role](./rbac_user_roles.html#create-a-new-user-role)
2. [Assign permissions to a user role](./rbac_user_roles.html#assign-permissions-to-a-user-role)
3. [Add a user to a role](./rbac_user_roles.html#add-a-user-to-a-user-role)

The RBAC permissions page has a full list of [available permissions](./rbac_permissions.html#available-permissions).

### Using token authentication

Before running an Orchestration job, you must generate an RBAC access token to authenticate to the Orchestration service. If you attempt to run a job without a token, PE will prompt you to supply credentials.

For information about generating a token with the CLI, see the documentation on [token-based authentication](./rbac_token_auth.html#generating-a-token-using-the-command-line-tool).

## Prepare your agents for orchestration services

When you use Orchestration services to configure multi-node applications in a Puppet environment (for example, in the `production` environment), you want the Puppet agents in that environment to rely on their cached catalogs until you run an orchestrator job that includes configuration changes for those agents. Puppet agents in such environments can still check in every thirty minutes and submit their facts, but they should rely on their cached catalogs and apply new configuration only when you run an orchestrator job.

**To prepare your agents for Orchestration services**:

>**Note**: These steps need to be performed only on agents you wish to use with Orchestration services.

1. If you’ve not already done so, [install your Puppet agents](./install_agents.html) and [sign the agent cert](./console_cert_mgmt.html#rejecting-and-approving-nodes).
2. Run Puppet on the new agents.

   Be sure you run Puppet on the new agents **before** assigning any application components to them or performing the next step.

3. In each agent’s `puppet.conf` file, add the following settings to the `[agent]` section:

   ~~~
   use_cached_catalog=true
   ~~~

   If you have many agents participating in Application Orchestration services, manually editing each agent's `puppet.conf` file to add these settings can be cumbersome. Instead, add the settings using one of the following methods:

   - From the command line on each agent machine, run:

     ~~~
     puppet config set use_cached_catalog true --section agent
     ~~~

   - Add an `ini_setting` resource in the `node default {}` section of the environment’s `site.pp`:

     ~~~
     ini_setting { 'use_cached_catalog':
       ensure  => present,
       path    => $settings::config,
       section => 'agent',
       setting => 'use_cached_catalog',
       value   => 'true',
     }
     ~~~

   Note that this last example adds this setting to **all** agents in that environment.

4. Run Puppet on the agents again to enforce this configuration.

## Static catalogs and Application Orchestration

Refer to the [static catalog documentation](./static_catalogs.html) for an overview on how static catalogs prevent file resource errors when running cached catalogs.


## Environment mismatches on Puppet job runs

In 2016.1.1, if you run an orchestration job for an environment (for example, "dev"), and the job contains an agent that is classified in the node classifier for a different environment (for example, "test"), the agent will detect the environment mismatch. When an agent detects a mismatch, it will abort the run, and the job output will reflect that.

This behavior applies to agents running PE 2016.1.1. If agents are running 2015.3.x, they will not detect the mismatch, and the agent may run in the wrong environment.

## Validating Application Orchestration code

When you're ready to validate Puppet code you intend to use as application orchestration code, you need to add the `--app_management` flag to the parser command. The full command to run is `puppet parser validate --app_management`.

Additionally, you can set `app_management=true` in the `[main]` or `[master]` section of `puppet.conf`, in which case you will not need to add the `--app_management` flag to validate your code.
