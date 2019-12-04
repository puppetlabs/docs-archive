# New features

These are the features added to PE 2019.0.

## Webhook connection available for Bitbucket configuration \(2019.0.4\)

When setting up a Code Manager webhook in Bitbucket Server 5.4 or later, you can now configure the connection as a "webhook" instead of a "hook" in Bitbucket configuration.

## Continuous Delivery for PE console installation \(2019.0.3\)

You can now install Continuous Delivery for PE directly from the console using a new **Integrations** page. Installation leverages a Bolt task requiring a limited set of paramaters, so you no longer have to install a separate module or dependencies. For details about installing Continuous Delivery for PE, see [Install Continuous Delivery for PE from the PE console](https://puppet.com/docs/continuous-delivery/2.x/install_pe_console.html) in the Continuous Delivery for PE documentation.

## `puppet infrastructure run` command \(2019.0.3\)

A new `puppet infrastructure run` command leverages built-in Bolt plans to perform certain PE management tasks, such as regenerating certificates and migrating from a split to a monolithic installation. To use the command, you must be able to connect using SSH from your master to any nodes that the command modifies. You can establish an SSH connection using key forwarding, a local key file, or by specifying keys in `.ssh/config` on your master. For information about available plans, run `puppet infrastructure run --help`.

## Enable a new HA replica using a failed master \(2019.0.3\)

After promoting a replica, you can use your old master as a new replica, effectively swapping the roles of your failed master and promoted replica.

## Support for BitBucket Server webhooks \(2019.0.3\)

Code Manager now uses BitBucket Server webhooks. The previous "Hooks" plugin is no longer required.

## Express installation \(2019.0.2\)

A new express install option lets you install PE using default settings, so you don't have to edit a `pe.conf` file before or during installation. At the end of the installation process, you're prompted to provide a console administrator password, which is the only user-required value.

## Node usage tracking \(2019.0.2\)

PE tracks your calendar month node usage and reports the total number of active nodes in your deployment. Active nodes include nodes that are present in PuppetDB and nodes without a Puppet agent that have had PE actions taken on them. Visit the **License** page to view your daily and monthly node usage, organized by date and agent status.

**Note:** The per-node pricing model for PE 2019.0 has not changed. Nodes with and without agents are licensed the same way. In addition, to support spikes in node usage, four days per calendar month you can exceed your licensed node count up to double the number of nodes you purchased. This increased number of nodes is called your *bursting limit*. You must buy more nodes for your license if you exceed the licensed node count within the bursting limit on more than four days per calendar month, or if you exceed your bursting limit at all. In these cases, license warnings appear in the console until you contact your Puppet representative. For more information on supporting licensed nodes, [Purchasing and installing a license key](purchasing_and_installing_a_license_key.md#).



## Windows agent installation from the console \(2019.0.1\)

Add Windows agents to target nodes from the console using the task `pe_bootstrap::windows`. For instructions, see [Install Windows agents](installing_agents.md#).

## Tasks via WinRM \(2019.0.1\)

You can use the Windows Remote Management \(WinRM\) protocol to run tasks on target nodes that do not have the Puppet agent installed. To learn how, see [Run a task over WinRM](running_tasks_in_the_console.md#).

## \*nix agent installation from the console \(2019.0.0\)

Add \*nix agents to target nodes from the console using the task pe\_bootstrap::linux. For instructions, see [Install agents from the console](installing_agents.md#).

## Scheduled tasks \(2019.0.0\)

You can schedule a job to run a task at a particular date and time. Set up a scheduled job from the console or with the endpoint `scheduled_jobs` that has been added to orchestrator API. For more information, see [Schedule a task](running_tasks_in_the_console.md#).

In addition, **Scheduled jobs** has been added to the list of permission types available for user roles. You add this permission to user roles that need to delete jobs that another user has scheduled.

## Tasks via SSH \(2019.0.0\)

You can use the SSH protocol to run tasks on target nodes that do not have the Puppet agent installed. To learn how, see [Run a task over SSH](running_tasks_in_the_console.md#).

## Support for cross-platform tasks \(2019.0.0\)

Puppet Enterprise supports cross-platform tasks. You can write a single task that has a Python or Bash implementation for Linux and a PowerShell implementation for Windows. For more information, see [Cross-platform tasks](writing_tasks.md#).

