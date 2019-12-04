---
layout: default
title: "Preparing orchestrator in the console"
canonical: "/pe/latest/orchestrator_prep_console.html"
---

The Puppet orchestrator is installed and configured as part of the PE console. 

Once you've installed PE, there are a few tasks you need to do to prepare your PE infrastructure before you use the orchestrator.

1. [Setting PE RBAC permissions orchestrator](#setting-pe-rbac-permissions-and-token-authentication-for-puppet-orchestrator)
2. [Enable cached catalogs for use with the orchestrator (optional)](#enable-cached-catalogs-for-use-with-the-orchestrator-optional)

## Setting PE RBAC permissions for orchestrator

Before you run any orchestrator jobs, you need to set the appropriate permissions in PE's role-based access control (RBAC) and establish token-based authentication.

Most users require the following permissions:

Type              | Permission                   | Definition
------------------|------------------------------|------------
Puppet agent     | Run Puppet on agent nodes. | The ability to run Puppet on nodes using the console or orchestrator. Instance must always be `"*"`. 
Nodes             | View node data from PuppetDB. | The ability to view node data imported from PuppetDB. Object must always be `"*"`.

The following links provide instructions for creating user roles and setting permissions:

1. [Create a new user role](./rbac_user_roles.html#create-a-new-user-role)
2. [Assign permissions to a user role](./rbac_user_roles.html#assign-permissions-to-a-user-role)
3. [Add a user to a role](./rbac_user_roles.html#add-a-user-to-a-user-role)

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


>**Note:** Refer to the [static catalog documentation](./static_catalogs.html) for an overview on how static catalogs prevent file resource errors when running cached catalogs.

## Disabling application management or orchestration services

Both application management and orchestration services are on by default in PE. If you need to disable these services, refer to [Disabling application management](./config_orchestration.html#disabling-application-management) and [Disabling orchestration services](./config_orchestration.html#disabling-orchestration-services).