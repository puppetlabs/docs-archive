---
author: Melissa Amos <melissa.amos@puppet.com\>
---

# PE release notes

These are the new features, enhancements, resolved issues, and deprecations in this version of PE.

## PE 2019.1.4

### Enhancements

#### Puppet ensures platform repositories aren't installed in order to prevent accidental agent upgrade

Previously, Bolt users who installed the Puppet 5 or 6 platform repositories could experience unsupported agent upgrades on managed nodes. With this release, Puppet ensures that the release packages for those platforms are not installed on managed nodes by enforcing `ensure => 'absent'` for the packages.

#### Windows install script optionally downloads a tarball of plug-ins

For Windows agents, the agent install script optionally downloads a tarball of plug-ins from the master before the agent runs for the first time. Depending on how many modules you have installed, bulk plug-in sync can speed agent installation significantly.

**Note:** If your master runs in a different environment from your agent nodes, you might see some reduced benefit from bulk plug-in sync. The plug-in tarball is created based on the plug-ins running on the master agent, which might not match the plug-ins required for agents in a different environment.

This feature is controlled by the setting `pe_repo::enable_windows_bulk_pluginsync` which you can configure in Hiera or in the console. The default setting for bulk plug-in sync is set to `false` \(disabled\).

#### `puppet infrastructure run` commands no longer require an authentication token

`puppet infrastructure run` commands that affect PuppetDB, including `migrate_split_to_mono` and `enable_ha_failover`, no longer require setting up token-based authentication as a prerequisite for running the command. By default, these commands use the master's PuppetDB certificate for authentication.

#### `puppet infrastructure run` commands provide more useful output

`puppet infrastructure run` commands, such as those for regenerating certificates or enabling high availability failover, now provide more readable output, making them easier to troubleshoot.

#### Calculations for PostgreSQL settings are fine-tuned

The `shared_buffers` setting uses less RAM by default due to improvements in calculating PostgreSQL settings. Previously, PostgreSQL settings were based on the total RAM allocated to the node it was installed on. Settings are now calculated based on total RAM less the default RAM used by PE services. As a result, on an 8GB installation for example, the default `shared_buffers` setting is reduced from ~2GB to ~1GB.



#### \*nix command for regenerating agent certificates includes a parameter for CRL cleanup

The `puppet infra run regenerate_agent_certificate` command includes a `clean_crl` parameter. Setting `clean_crl` to `true` cleans up the local CRL bundle. When you regenerate certificates for \*nix agents after recreating your certificate authority, you must include this parameter with the value set to `true`. If you're regenerating agent certificates without recreating the CA, you don't need to clean up the CRL.

#### Platform support

This version adds support for these platforms.

**Agent**

-   Fedora 31

### Resolved issues

#### Console was inaccessible on macOS Catalina using default certificates

Enhanced security requirements in macOS Catalina prevented accessing the console using the default certificate generated during installation.

#### `puppet infrastructure run` commands could fail if the agent was run with cron

`puppet infrastructure run` commands, such as those used for certain installation, upgrade, and certificate management tasks, could fail if the Puppet agent was run with cron. The failure occurred if the command conflicted with a Puppet run.

#### Mismatch between classifier classification and matching nodes for regexp rules

PuppetDB’s regular expression matching had surprising behaviors for structured fact value comparisons. For example, the structured fact `os` is a rule that matches `["~", "os", ":"]`. PuppetDB would unintentionally match every node that has the `os` structured fact because the regular expression was applied to the JSON encoded version of the fact value.

The classifier does not use PuppetDB for determining classification and regular expressions in the classifier rules syntax only support direct value comparisons for string types.

This caused issues in the console where the node list and counts for the "matching nodes" display sometimes indicated that nodes were matching even though the classifier would not consider them matching.

Now, the same criteria is applied to the displays and counts that the classifier uses. The output of the classifier’s rule translation endpoints makes queries that match the classifier behavior.

**Note:** This fix doesn't change the way nodes are classified, only how the console displays matching nodes.

## PE 2019.1.3

### Enhancements

#### The `puppet infrastructure run` command no longer requires the `caserver` parameter

The `caserver` parameter is no longer required for `puppet infrastructure run` commands that are run from your master.

### Resolved issues

#### Replicas tried to query PuppetDB on the primary master

In high availability installations, the replica was incorrectly configured to first send queries to the PuppetDB service on the primary master. The failover list has been corrected so that the replica now queries its own PuppetDB service first.

#### Rerunning the installer created the All Environments node group

If the installer was run for a second time due an issue such as a failed upgrade or a faulty agent lock, the All Environments node group was mistakenly created for the installation. This issue has been resolved, and the All Environments node group is only created for new installations.

#### Puppet run failures after a split installation with multiple PuppetDB instances was migrated to a monolithic installation

If your split PE installation architecture included multiple standalone PuppetDB instances, the `puppet infrastructure run migrate_split_to_mono` command could not create a PuppetDB instance in the new monolithic installation, and subsequent Puppet runs failed. The `puppet infrastructure run migrate_split_to_mono` command now exits with an error message if multiple PuppetDB instances are present in your split installation.

#### Console output was shown when installing in quiet mode

When running the installer in text mode, adding the `-q` option did not successfully activate quiet mode, and the installation process was logged in the console.

#### Package versions were not reset correctly after failed upgrade

If an agent run was in progress as an upgrade began, and consequently the installer failed because it could not acquire an agent lock, the installer did not roll back the relevant packages to the pre-upgrade PE version.

#### `puppet infra run` plans caused Puppet agent service settings to be ignored

In some cases, plans used in `puppet infrastructure run` commands forced the Puppet agent service to run after the plan was complete, even if you had previously disabled the service. The impacted plans now reset the Puppet agent service to the state it was in before the plan was run.

#### Certificate backup directories could be overwritten

When a certificate regeneration command was run multiple times, certificate backup directories could be unintentionally overwritten. To solve this issue, certificate backup directories are now uniquely named using a time stamp.

#### Upgrade attempts failed when a Puppet run was in progress

If a Puppet run began while the installer was attempting to upgrade PE, conflicts and failures occurred. The installer now checks for Puppet runs before beginning an upgrade, and stops the upgrade if one is in progress.

## PE 2019.1.1

### Enhancements

#### Infrastructure-only CRL disable option

You can now disable auto-creation of the infrastructure-only CRL, which was enabled by default in a previous release. To disable the option, set `puppet_enterprise::profile::certificate_authority::enable_infra_crl: false` in Hiera or `pe.conf`, or in the console, in the **PE Certificate Authority** node group, in the **puppet\_enterprise::profile::certificate\_authority** class, set the **enable\_infra\_crl** parameter to **false**.

#### Specify alternate DNS names when regenerating certificates

When regenerating agent or master certificates, you can now pass an optional `dns_alt_names` parameter to the Bolt task.

#### Report details sorted by event

Events in report details are now sorted by event type rather than resource name, so you can locate interesting events more reliably.

### Resolved issues



#### Puppet Server fails when it finds a lockfile

Prior to this release, Puppet Server failed if it found a lockfile in the file sync directories. Such lockfiles could be left in file sync directories if Puppet Server was terminated while file sync was syncing code. Now, Puppet Server instead cleans up this lockfile as part of startup.

If you run multiple file sync processes that must respect each other's lockfiles, disable this behavior by setting the `puppet_enterprise::master::file_sync::clean_lock_files_at_startup hiera` key to `false`.

#### ssh-keygen produces unusable key on RHEL8

To enable newer SSH key formats with Code Manager on Red Hat Enterprise Linux 8 \(RHEL8\), client-side C library libssh2 has been updated to a version that supports them.

#### Unable to view link to Unsigned certs

In PE 2019.1.0 the **Unsigned certs**navigation link was intermittently visible for users who had permission to accept and reject certificate requests. This has been fixed.

#### Issues discarding changes to RBAC member groups

In previous versions of PE, if you made changes to member groups for a selected user role and later clicked **Discard changes** instead of **Commit changes**, the changes were not cleared properly. This has been fixed.

#### When removing replicas, empty parameters were added to the PE Agent group

When you run `puppet infrastructure forget` on a high availability deployment that uses load-balanced compile masters, `server_list` and `pcp_broker_list` parameters are no longer added to the `puppet_enterprise::profile::agent` class with their values set to empty arrays.

#### Replicas did not receive new or updated packages

When new packages were introduced on the master, whether as the result of a PE upgrade or the introduction of new agents,existing replicas did not receive the new packages.

#### Upgrade failures caused the `pe-installer` package to uninstall

If any failure occurred during your upgrade of PE, the `pe-installer` package was automatically uninstalled.

#### Reference to plaintext password in unused orchestrator configuration file

An unused default `orchestrator.conf` file contained a reference to a plaintext password entry and value. This entry has been removed.

#### Transport preference incorrectly reported in task target table

On the **Task** page in the console, under certain conditions, the **Connection** column incorrectly displayed the inventory transport instead of the Puppet agent transport.

#### Web-based installation support for Red Hat Enterprise Linux \(RHEL\) 8

Red Hat Enterprise Linux \(RHEL\) 8 is now included in the web-based installation method's list of supported operating systems.

#### Reinitializing a replica after upgrade failed

When upgrading a replica, the reinitialize command hung and failed after five minutes depending on the order of services in your `/etc/puppetlabs/client-tools/services.conf` file.

#### Enabling a new replica using a previous master failed with autosign enabled

The `puppet infrastructure run enable_ha_failover` command, which lets you enable a failed master as a new replica, includes a step for signing the node's certificate. With autosign enabled, an unsigned certificate couldn't be found, and the command errored out.

#### PE-PostgreSQL couldn't be enabled or disabled on Ubuntu 18.04

On Ubuntu 18.04 masters, a bug in systemd 237 prevented enabling or disabling the PE-PostgreSQL service using `systemctl`.

#### Regenerating master certificates failed if the command conflicted with automatic backups

The `puppet infrastructure run regenerate_master_certificate` command failed if it ran at the same time as automatic configuration backups, triggering an error about pre-existing key files.

#### Regenerating agent certificates failed with autosign enabled

The `puppet infrastructure run regenerate_agent_certificate` command includes a step for signing the node's certificate. With autosign enabled, an unsigned certificate couldn't be found, and the command errored out.

#### Backup failed with File changed as we read it error

If file sync tried to change files while the backup command was archiving files, the backup command errored and failed.

#### Restore failed if `/tmp` folder was too small

When restoring a master using `puppet backup restore`, the `/tmp` folder was always used as the temporary location for unpacking PostgreSQL dumps. If `/tmp` didn't have enough space to hold these dumps, the restore failed. Additionally, attempting to use a different temp directory by setting the `TMPDIR` environment variable did not work correctly.

#### Restore reset master DNS altnames

When restoring a master using `puppet backup restore`, `pe_install::puppet_master_dnsaltnames` was reset as an array with only the certname of the restore host.

#### Usernames appear as Base64 strings

Under some circumstances when using an LDAP server, usernames that contained extended characters would incorrectly appear as a Base64 encoded string. For example, a username spelled with a German umlaut, Schröder, would appear in the string format **U2NocsO2ZGVyDQo=**. After upgrading to PE 2019.1.1, users impacted by this issue must log out and log back in to see their usernames correctly spelled.

## PE 2019.1.0

### New features

#### Schedule recurring tasks

From the console, you can schedule a job to run a task at regular intervals, such as hourly, daily, or weekly. For more information, see [Schedule a task](running_tasks_in_the_console.md#) and [Schedule a Puppet run](running_puppet_on_demand_in_the_console.md#).

#### Continuous Delivery for PE console installation

You can now install Continuous Delivery for PE directly from the console using a new **Integrations** page. Installation leverages a Bolt task requiring a limited set of parameters, so you no longer have to install a separate module or dependencies. For details about installing Continuous Delivery for PE, see [Install Continuous Delivery for PE from the PE console](https://puppet.com/docs/continuous-delivery/2.x/install_pe_console.html) in the Continuous Delivery for PE documentation.

#### `puppet infrastructure run` command

A new `puppet infrastructure run` command leverages built-in Bolt plans to perform certain PE management tasks, such as regenerating certificates and migrating from a split to a monolithic installation. To use the command, you must be able to connect using SSH from your master to any nodes that the command modifies. You can establish an SSH connection using key forwarding, a local key file, or by specifying keys in `.ssh/config` on your master. For information about available plans, run `puppet infrastructure run --help`.

#### Enable a new HA replica using a failed master

After promoting a replica, you can use your old master as a new replica, effectively swapping the roles of your failed master and promoted replica.

#### Add nodes without agents to Puppet Enterprise

Using the new **Inventory** option on the console, you can add nodes to your Puppet Enterprise deployment without installing the Puppet agent. When you add nodes and their credentials to the inventory, the information is securely stored and made available in the console through remote connections \(SSH or WinRM\). Authorized users can then run tasks on these nodes without re-entering credentials. For more information, see [Adding and removing agentless nodes](adding_and_removing_nodes_inventory.md#).

### Enhancements

#### Remote tasks

You can now run tasks on a proxy target that remotely interacts with the real target, as defined by the `run-on` option. Remote tasks are useful for targets like network devices that have limited shell environments, or cloud services driven only by HTTP APIs. Connection information for non-server targets, like HTTP endpoints, can be stored in inventory.

#### Simplified Code Manager control repo configuration

Setting up control repositories for Code Manager no longer requires manually creating an SSH directory and configuring permissions on the key pair and directory. These steps have been automated with the `puppet infrastructure configure` command.

#### Improved handling of server settings

Server settings specified in `puppet.conf` are now used in this order:

-   `server_list`

-   `server`


In high availability installations which rely on the `server_list` setting, this order prevents unexpected behavior if you promote a replica.

#### Improved RBAC API log messages

The RBAC service log entries for revoked users attempting to log in now includes the username and UUID.

#### Infrastructure terminology changes

With this version, we've unified infrastructure terminology across all installation types. We now call compile masters compilers to reflect their role: compiling catalogs. Similarly, we call the master a master, whether or not your installation includes compilers. In high availability installations, the node that replicates data from the master is simply a replica or master replica.

R.I.P. MoM, master of masters, and primary master replica.

#### Platform support

This version adds support for these platforms.

**Master**

-   Enterprise Linux 8

**Note:** Enterprise Linux 8 support for agents was added in previous Z releases.


### Deprecations and removals

#### Split and large environment installations

The split and large environment installations, where the master, console, and PuppetDB were installed on separate nodes, are no longer recommended. Because compilers do most of the intensive computing, installing the console and PuppetDB on separate nodes doesn't substantially improve load capability, and adds unnecessary complexity.

For new installations, we now recommend only monolithic configurations, where the infrastructure components are installed on the master. You can add one or more compilers and a load balancer to this configuration to expand capacity up to 20,000 nodes, and for even larger installations, you can install standalone PE-PostgreSQL on a separate node. For details about current installation configurations, see [Choosing an architecture](choosing_an_architecture.md). For instructions on migrating from a split installation to a monolithic installation, see [Migrate from a split to a monolithic installation](upgrading_pe.md#).

#### `pe_accounts` module

The `pe_accounts` module has been removed. The module was used internally to manage PE users but was superseded several versions ago by the `puppetlabs/accounts` module. If you're using the `pe_accounts` module for account management, migrate to the `puppetlabs/accounts` module as soon as possible. As a short term workaround, you can copy the `pe_accounts` module from an existing PE installation or from the `pe-modules` package inthe PE installer tarball and place the module in your own modulepath. 

#### TLSv1 and v1.1

TLSv1 and TLSv1.1 is now disabled by default to comply with security regulations. You must [enable TLSv1](enable_tlsv1.md) to install agents on these platforms:

-   AIX

-   CentOS 5

-   RHEL 5

-   SLES 11

-   Solaris 10

-   Windows Server 2008r2


#### `puppet_enterprise` parameters

These deprecated parameters have been removed in this version:

-   `mcollective_middleware_hosts`
-   `mcollective`
-   `mcollective_middleware_port`
-   `mcollective_middleware_user`
-   `mcollective_middleware_password`
-   `activity_database_user`
-   `classifier_database_user`
-   `orchestrator_database_user`
-   `rbac_database_user`
-   `dashboard_port`
-   `dashboard_database_name`
-   `dashboard_database_user`
-   `dashboard_database_password`

### Resolved issues



#### Puppet file permissions on Windows were modified with every run

Changes to how Puppet handled system permissions caused permissions for Windows file resources to be rewritten with each run.

Puppet now treats owner and group on file resources as in-sync in either of these scenarios:

-   owner and group are not set in the resource

-   owner and/or group are set to the system user on the running node **and** the system user is set to full control


You can specifically configure the system user to less than full control by setting the owner and/or group parameters to `SYSTEM` in the file resource. In this case, Puppet emits a warning, because setting `SYSTEM` to less than full control might have unintended consequences, but it does not modify the permissions.

#### Setting node group environment required Edit configuration data permission

To allow a user role to set a node group environment, users previously had to add the permission **Edit configuration data** in addition to **Set environment**. The permission **Set environment** alone is now enough to allow a user to change the environment.

