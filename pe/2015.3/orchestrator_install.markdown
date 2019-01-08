---
layout: default
title: "Installing and Configuring the Puppet orchestrator"
canonical: "/pe/latest/orchestrator_install.html"
---


The Puppet orchestrator is bundled in the [pe-client-tools package](./install_pe_client_tools.html). The pe-client-tools package is included in the PE installation tarball and, when you install PE, it is automatically installed on the same node as the Puppet master. However, you can install the pe-client-tools, including the Puppet orchestrator, on a separate workstation that isn’t managed by Puppet Enterprise.  

When you run the orchestrator from a separate workstation, you don't need to SSH into the Puppet master to execute commands. Your permissions to take action and see information is controlled by PE role-based access control (RBAC) and your activity is logged under your username rather than as `root` or the `pe-puppet` user.      

Follow these steps, detailed below, to install and configure the Puppet orchestrator: 

1. [Install the PE client tools package](#install-the-pe-client-tools), which includes the orchestrator client.
2. [Enable the Orchestration service](#enabling-the-orchestration-service).
3. [Configure the Puppet Orchestrator Client](#configure-the-puppet-orchestrator-client) 
4. [Set PE RBAC Permissions and Token Authentication for Puppet Orchestrator](#set-pe-rbac-permissions-and-token-authentication-for-puppet-orchestrator)
5. [Prepare Your Agents for Orchestration Services](#prepare-your-agents-for-orchestration-services)

## Install the PE Client Tools

If you have not already installed the PE client tools package, follow the steps in [Installing the PE client tools package](./install_pe_client_tools.html).

## Enable the Application Orchestration Service and Orchestrator Client

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

## Configure the Puppet orchestrator Client 

You can create a configuration file for the Orchestration client that will allow you to run commands from the CLI without having to pass additional flags. 

The configuration file for the orchestrator client must be named `orchestrator.conf`, and must be stored in the following location in the user’s home directory: `~/.puppetlabs/etc/puppet/orchestrator.conf`

The config file is formatted in JSON. For example:

~~~JSON
{
  "options" : {
    "url": "https://master.example.com:8143",
    "environment": "production"
  }
}
~~~

### Config File Settings

The orchestrator client config file (`orchestrator.conf`) can take the following settings:

- **url**: `https://{PUPPET MASTER HOSTNAME}:8143`---the URL that points to the Puppet master and the port used to communicate with the Orchestration service.
- **environment**: `{ENVIRONMENT NAME}`---the environment used when you issue commands with Puppet orchestrator. This will default to the `production` environment if you don't set it to another environment.
- **cacert**: `/etc/puppetlabs/puppet/ssl/certs/ca.pem`---the path for the Puppet Enterprise CA cert.
- **color**: (set to `true` or `false`)---determines whether the orchestrator output will use color.
- **verbose**: (set to `true` or `false`)---determines whether the orchestrator output will be verbose.


## Set PE RBAC Permissions and Token Authentication for Puppet Orchestrator

### Setting RBAC Permissions

Before running any orchestrator jobs, make sure the correct role and permissions are set for any user who will run Orchestration jobs. The following links provide instructions:

1. [Create a New User Role](./rbac_user_roles.html#create-a-new-user-role)
2. [Assign Permissions to a User Role](./rbac_user_roles.html#assign-permissions-to-a-user-role)
3. [Add a User to a Role](./rbac_user_roles.html#add-a-user-to-a-role)

The RBAC permissions page has a full list of [available permissions](./rbac_permissions.html#available-permissions).

### Using Token Authentication

Before running an Orchestration job, you must generate an RBAC access token to authenticate to the Orchestration service. If you attempt to run a job without a token, PE will prompt you to supply credentials. 

For information about generating a token with the CLI, see the documentation on [Token-Based Authentication](./rbac_token_auth.html#generating-a-token-using-the-command-line-tool).

## Prepare Your Agents for Orchestration Services

When you use Orchestration services to configure multi-node applications in a Puppet environment (for example, in the `production` environment), you want the Puppet agents in that environment to rely on their cached catalogs until you run an orchestrator job that includes configuration changes for those agents. Puppet agents in such environments can still check in every thirty minutes and submit their facts, but they should rely on their cached catalogs and apply new configuration only when you run an orchestrator job.

**To prepare your agents for Orchestration services**:

>**Note**: These steps need to be performed only on agents you wish to use with Orchestration services. 

1. If you’ve not already done so, [install your Puppet agents](./install_agents.html) and [sign the agent cert](./console_cert_mgmt.html#rejecting-and-approving-nodes).
2. Run Puppet on the new agents. 

   Be sure you run Puppet on the new agents **before** assigning any application components to them or performing the next step. 

3. In each agent’s `puppet.conf` file, add the following settings to the `[agent]` section:

   ~~~
   use_cached_catalog=true
   pluginsync=false
   ~~~

   If you have many agents participating in Orchestration services, manually editing each agent's `puppet.conf` file to add these settings can be cumbersome. Instead, add the settings using one of the following methods:
   
   - From the command line on each agent machine, run:
   
     ~~~
     puppet config set use_cached_catalog true --section agent
     puppet config set pluginsync false --section agent
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

     ini_setting { 'pluginsync':
       ensure  => present,
       path    => $settings::config,
       section => 'agent',
       setting => 'pluginsync',
       value   => 'false',
     }
     ~~~
  
   Note that this last example adds this setting to **all** agents in that environment.

4. Run Puppet on the agents again to enforce this configuration.

## Validating Application Orchestration Code

When you're ready to validate Puppet code you intend to use as application orchestration code, you need to add the `--app_management` flag to the parser command. The full command to run is `puppet parser validate --app_management`.

<!-- Ethnio Activation Code -->
<script type="text/javascript" language="javascript" src="//ethn.io/77154.js" async="true" charset="utf-8"></script>