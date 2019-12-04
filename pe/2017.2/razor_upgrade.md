---
layout: default
title: "Upgrading Razor"
canonical: "/pe/latest/razor_upgrade.html"

---

If you used Razor in a previous Puppet Enterprise (PE) environment, upgrade Razor to keep your PE and Razor versions synched.

After a PE upgrade, your `pe_razor` class continues to operate normally, but we recommend upgrading Razor as soon as possible to avoid unintended effects.

> **Note**: If nodes are actively provisioning during upgrade, provisioning might fail. You can resume provisioning after `pe-razor-server` restarts and -- for upgrade from 3.8.x only -- after a Puppet run completes.

The upgrade process varies by PE version.

## Upgrade from 3.8.x to later versions

An upgrade script handles upgrading Razor from PE 3.8.x to any later version. The script carries out these tasks:

* Performs pre-verification, including checking that:
    * The script is being run as root.
    * The `puppet-agent` class is installed. The script exits if not.
    * The Razor node has sufficient disk space available. The host's disk space must be at least three times the size of the Razor database.
* Shuts down the `pe-puppet-agent` (agent) and `pe-razor-server` services.
* Backs up the `pe-razor-server` PostgreSQLpostgres database.

	**Note**: This might take up to several minutes for larger databases.

* Shuts down the `pe-postgresql` server.
* Replaces existing software repository with the new, downloaded repository.
* Moves the following files to align with the updated file system layout:
	* `/etc/puppetlabs/razor` => `/etc/puppetlabs/razor-server`
	* `/opt/puppet/var/razor/repo` => `/opt/puppetlabs/server/data/razor-server/repo-store`
* Installs software packages.
* Completes a Puppet agent run to initialize new postgres databases and tablespaces.
* Starts `pe-postgresql` services and restores the backed up database.
* Starts the `pe-razor-server` service.
* Starts a Puppet agent run, then starts Puppet.

### Before you begin

Follow these steps to prepare to upgrade:

* Upgrade your [monolithic](./upgrade_mono.html) or [split](./upgrade_split.html) PE infrastructure, including the Puppet master, PostgreSQL database, and console.

* Verify that the Razor server has access to the internet.

  **Tip**: If you want to upgrade Razor offline, follow the instructions [here](./razor_install.html#install-the-razor-server).

* Verify that no services are active on the agent or Razor server. Services are stopped during upgrade.

* Ensure that no custom or modified tasks, brokers, or hooks exist inside `/opt/puppetlabs`. If they do, move them to a directory in the appropriate class parameter (`task_path`, `broker_path`, or `hook_path`) of the `pe_razor` class. By default, the paths for these objects are:

     * Tasks -- `/etc/puppetlabs/razor-server/tasks`
     * Brokers -- `/etc/puppetlabs/razor-server/brokers`
     * Hooks -- `/etc/puppetlabs/razor-server/hooks`

### Run the upgrade script

The Razor upgrade script upgrades your Razor server node to match the version of PE you're running.

1. SSH into your `razor-server` node, then run `sudo bash /opt/puppet/razor/upgrade/upgrade.bash`.

## Upgrade from 2015.2.x or later

Upgrading from 2015.2.x or later is a mostly automated process that replaces the software repository, installs software packages, and migrates the Razor database.

Upgrading to 2016.2 or later requires manual migration of any custom configuration from your config.yaml file to class parameters in the `pe_razor` class.

> **Before you begin:** If you're upgrading to PE 2016.2 or later and you've modified your config.yaml file -- for example, by changing `protect_new_nodes` or customizing tasks, brokers, or hooks -- make a note of the modified settings.

1. Upgrade the Puppet master.
2. Upgrade the agent on the Razor server node.

   The `pe-razor-server` service automatically restarts.

3. (Optional) If you're upgrading to 2016.2 or later, transfer any customized configurations to class parameters in the `pe_razor` class.

   **Note**: To prevent accidentally overwriting machines during upgrade, the default for `protect_new_nodes` was changed to `true` in PE 2016.2 and later. If your environment and workflows rely on provisioning all new nodes, you must manually change `protect_new_nodes` to `false` after upgrade, then [run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes) and restart the pe-razor-server service.

> **Tip**: You can run `razor --version` to verify that the upgrade was successful.


