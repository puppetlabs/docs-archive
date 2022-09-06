# New features

These are the features added to PE 2018.1.x.

## Webhook connection available for Bitbucket configuration \(2018.1.8\)

When setting up a Code Manager webhook in Bitbucket Server 5.4 or later, you can now configure the connection as a "webhook" instead of a "hook" in Bitbucket configuration.

## Continuous Delivery for PE console installation \(2018.1.8\)

You can now install Continuous Delivery for PE directly from the console using a new **Integrations** page. Installation leverages a Bolt task requiring a limited set of parameters, so you no longer have to install a separate module or dependencies. For details about installing Continuous Delivery for PE, see [Install Continuous Delivery for PE from the PE console](https://puppet.com/docs/continuous-delivery/2.x/install_pe_console.html) in the Continuous Delivery for PE documentation.

## `puppet infrastructure run` command \(2018.1.8\)

A new `puppet infrastructure run` command leverages built-in Bolt plans to perform certain PE management tasks, such as regenerating certificates and migrating from a split to a monolithic installation. To use the command, you must be able to connect using SSH from your master to any nodes that the command modifies. You can establish an SSH connection using key forwarding, a local key file, or by specifying keys in `.ssh/config` on your master. For information about available plans, run `puppet infrastructure run --help`.

## Enable a new HA replica using a failed master \(2018.1.8\)

After promoting a replica, you can use your old master as a new replica, effectively swapping the roles of your failed master and promoted replica.

## Schedule tasks \(2018.1.5\)

You can schedule a job to run a task at a particular date and time. Set up a scheduled job from the console or with the endpoint `scheduled_jobs` that has been added to orchestrator API. For more information, see [Schedule a task](running_tasks_in_the_console.md#).

In addition, **Scheduled jobs** has been added to the list of permission types available for user roles. You add this permission to user roles that need to delete jobs that another user has scheduled. 

## Delete user roles \(2018.1.3\)

You can delete a user role through the console \(**Access control** \> **User roles**\). Delete user roles that your organization no longer needs and remove the permissions that the role has given users.

## API endpoint for tracking multiple jobs \(2018.1.2\)

The commands endpoint `plan_finish`has been added to orchestrator API. You use this endpoint to track jobs that are run together as part of a plan. \(A plan combines multiple tasks and runs them with a single command. For more information, see the docs for [Bolt](https://puppet.com/docs/bolt/0.x/bolt.html).\)

## API endpoint for checking Code Manager code deployment status \(2018.1.2\)

This release adds a `/deploys/status` endpoint for Code Manager. Use this endpoint to check the status of the code deployments that Code Manager is processing. For details about the endpoint, see the Code Manager API docs.

## Configure expiration for inactive user accounts \(2018.1.2\)

The parameters `account-expiry-days` and `password-reset-expiration` were added to the [RBAC service configuration](rbac_api_v1_config.md#). You use these parameters to specify the duration, in days, before a user's inactive account expires, and how often, in minutes, the application checks for idle user accounts.

## Backup and restore functions \(2018.1.0\)

This release introduces Puppet Enterprise backup and restore functions. Back up your PE infrastructure, including Puppet code, configuration, PuppetDB, and certificates, allowing you to more easily migrate to a new master or recover from system failures.

## Role-based access to tasks \(2018.1.0\)

Tasks have been added to the list of permission types available for user roles. You assign task permissions to limit user access to specific tasks that run on all nodes or a selected node group.

## API endpoints for tracking multiple jobs \(2018.1.0\)

Two commands endpoints have been added to orchestrator API: `plan_start` and `plan_task`. You use these endpoints to track jobs run together as part of a plan. \(A plan combines multiple tasks and runs them with a single command. For more information, see the docs for [Bolt](https://puppet.com/docs/bolt/0.x/bolt.html).\)

**Related information**  


[Backing up and restoring Puppet Enterprise](backing_up_and_restoring_pe.md)

[User permissions](rbac_permissions_intro.md#)

