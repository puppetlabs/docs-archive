---
layout: default
title: "Upgrading Razor"
canonical: "/pe/latest/razor_upgrade.html"

---

If you used Razor in a previous Puppet Enterprise (PE) environment, upgrade Razor to keep your PE and Razor versions synched.

After a PE upgrade, your `pe_razor` module continues to operate normally, but we recommend upgrading Razor as soon as possible to avoid unintended effects.

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

* [Upgrade your PE infrastructure](./install_upgrading.html), including the Puppet master, PostgreSQL database, and console.

* Verify that the Razor server has access to the internet.

  **Tip**: If you want to upgrade Razor offline, follow the instructions [here](./razor_install.html#install-the-razor-server).

* Verify that no services are active on the agent or Razor server. Services are stopped during upgrade.

* Ensure that no custom tasks, brokers, or hooks exist inside `/opt/puppet`. If they do, move them to another location, as described below:
  * **Tasks** -- Edit `task_path` in `/etc/puppetlabs/razor/config.yaml` to include the new path, for example: `task_path: /etc/puppetlabs/razor/tasks:tasks`.
  * **Brokers** -- Edit `broker_path` in `/etc/puppetlabs/razor/config.yaml` to include the new path, for example: `broker_path: /etc/puppetlabs/razor/brokers:brokers`.
  * **Hooks** -- Edit `hook_path` in `/etc/puppetlabs/razor/config.yaml` to include the new path, for example: `hook_path: /etc/puppetlabs/razor/hooks:hooks`.

### Run the upgrade script

The Razor upgrade script upgrades your Razor server node to match the version of PE you're running.

1. SSH into your `razor-server` node, then run `sudo bash /opt/puppet/razor/upgrade/upgrade.bash`.

## Upgrade from 2015.2.x or later

Upgrading from 2015.2.x or later is a mostly automated process that replaces the software repository, installs software packages, and migrates the Razor database.

1. Upgrade the Puppet master.
2. Upgrade the agent on the Razor server node.

The `pe-razor-server` service automatically restarts.

> **Tip**: You can run `razor --version` to verify that the upgrade was successful.


