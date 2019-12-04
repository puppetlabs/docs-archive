# Resolved issues

These are the issues resolved in PE 2019.0.

## Issues discarding changes to RBAC member groups \(2019.0.4\)

In previous versions of PE, if you made changes to member groups for a selected user role and later clicked **Discard changes** instead of **Commit changes**, the changes were not cleared properly. This has been fixed.

## When removing replicas, empty parameters were added to the PE Agent group \(2019.0.4\)

When you run `puppet infrastructure forget` on a high availability deployment that uses load-balanced compile masters, `server_list` and `pcp_broker_list` parameters are no longer added to the `puppet_enterprise::profile::agent` class with their values set to empty arrays.

## Replicas did not receive new or updated packages \(2019.0.4\)

When new packages were introduced on the master, whether as the result of a PE upgrade or the introduction of new agents,existing replicas did not receive the new packages.

## Upgrade failures caused the `pe-installer` package to uninstall \(2019.0.4\)

If any failure occurred during your upgrade of PE, the `pe-installer` package was automatically uninstalled.

## Reinitializing a replica after upgrade failed \(2019.0.4\)

When upgrading a replica, the reinitialize command hung and failed after five minutes depending on the order of services in your `/etc/puppetlabs/client-tools/services.conf` file.

## Enabling a new replica using a previous master failed with autosign enabled \(2019.0.4\)

The `puppet infrastructure run enable_ha_failover` command, which lets you enable a failed master as a new replica, includes a step for signing the node's certificate. With autosign enabled, an unsigned certificate couldn't be found, and the command errored out.

## Regenerating master certificates failed if the command conflicted with automatic backups \(2019.0.4\)

The `puppet infrastructure run regenerate_master_certificate` command failed if it ran at the same time as automatic configuration backups, triggering an error about pre-existing key files.

## Regenerating agent certificates failed with autosign enabled \(2019.0.4\)

The `puppet infrastructure run regenerate_agent_certificate` command includes a step for signing the node's certificate. With autosign enabled, an unsigned certificate couldn't be found, and the command errored out.

## Backup failed with File changed as we read it error \(2019.0.4\)

If file sync tried to change files while the backup command was archiving files, the backup command errored and failed.

## Restore failed if `/tmp` folder was too small \(2019.0.4\)

When restoring a master using `puppet backup restore`, the `/tmp` folder was always used as the temporary location for unpacking PostgreSQL dumps. If `/tmp` didn't have enough space to hold these dumps, the restore failed. Additionally, attempting to use a different temp directory by setting the `TMPDIR` environment variable did not work correctly.

## Restore reset master DNS altnames \(2019.0.4\)

When restoring a master using `puppet backup restore`, `pe_install::puppet_master_dnsaltnames` was reset as an array with only the certname of the restore host.

## Usernames appear as Base64 strings \(2019.0.4\)

Under some circumstances when using an LDAP server, usernames that contained extended characters would incorrectly appear as a Base64 encoded string. For example, a username spelled with a German umlaut, Schröder, would appear in the string format **U2NocsO2ZGVyDQo=**. After upgrading to PE 2019.0.4, users impacted by this issue must log out and log back in to see their usernames correctly spelled.

## Puppet file permissions on Windows were modified with every run \(2019.0.3\)

Changes to how Puppet handled system permissions caused permissions for Windows file resources to be rewritten with each run.

Puppet now treats owner and group on file resources as in-sync in either of these scenarios:

-   owner and group are not set in the resource

-   owner and/or group are set to the system user on the running node **and** the system user is set to full control


You can specifically configure the system user to less than full control by setting the owner and/or group parameters to`SYSTEM` in the file resource. In this case, Puppet emits a warning, because setting `SYSTEM` to less than full control might have unintended consequences, but it does not modify the permissions.

## White space and non-UTF-8 characters in package names and versions triggered invalid byte sequence error \(2019.0.3\)

When collecting package names and versions, the Windows Package Inspector replaced white spaces and non-UTF-8 characters with "�", causing Puppet runs to fail or submit data that PuppetDB rejected.

## Restoring to a former agent node with a signed certificate in the master's `ssl/ca/signed` directory failed \(2019.0.3\)

If you attempted to back up data to a former agent node whose node certificate still existed in the `/etc/puppetlabs/puppet/ssl/ca/signed` directory that was part of the backup tarball, the restore failed when it attempted to regenerate a new certificate for the master.

## Upgrade could trigger non-blocking errors \(2019.0.3\)

Upgrade triggered the error -lt: unary operator expected when verification of the PostgreSQL version number encountered a "."

## After upgrade, agent runs triggered a cyclical dependency \(2019.0.3\)

After upgrade, a Puppet run could trigger a cyclical dependency if any of the folders listed below were managed with a file resource on a PE infrastructure node.

-   RHEL — /etc/yum.repos.d

-   Ubuntu — /etc/apt/sources.list.d

-   SUSE Linux Enterprise Server — /etc/zypp/repos.d


You can now manage YUM and APT repositories on infrastructure nodes without encountering cyclical dependency errors from `puppet_enterprise::repo::config`.

## Classifier inconsistently applies rules to escaped numerical variables \(2019.0.3\)

Previously, the node classifier service API inconsistently applied escaped numerical variables to string parameters in class declarations. Where these variables were entered by themselves \(`"\\$1"`\) they were correctly processed. Where these variables were nested in key-value pairs \(`{"key" : "a value \\$1"}`\) they were incorrectly processed as object values. Escaped numerical variables are now processed consistently and correctly.

## PE uninstaller no longer uninstalls related products, like Bolt \(2019.0.2\)

The PE uninstaller now purges only `/opt/puppetlabs/puppet` and `/opt/puppetlabs/server`, leaving behind `/opt/puppetlabs` if there are any other files remaining in this directory. Leaving this directory in place, as necessary, prevents inadvertently removing other Puppet products, such as Bolt.

## Pre-upgrade checks timed out in large installations \(2019.0.2\)

The upgrader checks for existingMCollective agents and halts upgrade if any are found. In large installations \(tens of thousands of nodes\) the MCollective check could exceed the default 30-second `server_url_timeout` allowed by PuppetDB, effectively blocking the upgrade.

## Script installation failed on Solaris 11.4 agents \(2019.0.2\)

Due to a change in operating system identification for Solaris 11.4, agent installation using the install script failed with an error indicating the installation method wasn't supported.

## Master and replica weren't pinned to appropriate node groups after promotion \(2019.0.2\)

When a replica was promoted, the retired master remained pinned to the `PE Database` node group, and the newly promoted master was *not* pinned to the group. Consequently, the database was not automatically managed after you promoted a replica.

## Package updates failed on promoted replicas \(2019.0.2\)

On promoted HA replicas, the managed package repository configuration contains out-of-date references to the old primary master. This causes package updates, like `yum update`, to fail because the repository is pointing to the old master. In general, the packaging configuration artifacts for PE infrastructure are now named `puppet_enterprise` across all platforms. The temporary artifacts created by the `pe_repo` module for agent installation are named `pe_repo`.

## Permissions weren't set correctly on global Hiera file \(2019.0.2\)

On systems with a umask other than 000, the installation process didn't correctly set permissions on the global Hiera file at `/etc/puppetlabs/puppet/global_hiera.yaml`.

## Package and service tasks run over SSH and WinRM relied on Puppet agent \(2019.0.2\)

The package and service tasks included with PE use implementations that do not require the Puppet agent be present on target nodes.

## `bolt-server` clears temporary directory during cache purging \(2019.0.2\)

When `pe-bolt-server` purges the task cache directory, the temporary location where task downloads are staged is preserved. Previously the staging directory was deleted as part of the purge and this resulted in task download errors.

## Deploying additional code or environments blocked Puppet Server startup \(2019.0.1\)

Using Code Manager to deploy additional code and environments increased the amount of time it took to start, restart, or reload the pe-puppetserver service, to the point that these operations could time out. The increase in startup time was caused by a diagnostic that checked every file in every environment and logged any modifications. This diagnostic has been removed.

## Running `r10k deploy` on Bionic triggered a Ruby bug \(2019.0.1\)

Running either r10k 2.6 or 3.0 with the shellgit provider on Bionic could trigger a Ruby bug when running `r10k deploy`. This bug was more likely the more environments you deploy.

## Slow response times when filtering nodes \(2019.0.1\)

Previously, when you filtered nodes based on fact values \(a feature available from the Overview and Classification pages\) in an environment with a large number of nodes, it would result in slow response times and, in some cases, cause timeout errors. Now when you filter nodes, a limited number of concurrent node queries is run, which improves performance.

## Enumerated task parameter values not available in drop-down lists \(2019.0.1\)

When a single task parameter value that used the enumerated data type contained a hyphen \(-\), none of the set values appeared in drop-down lists. For example, these set values `Enum[install, status-only, uninstall]` would not appear because of the value **status-only**. This has been fixed.

## Fedora 26 and 27 agent upgrade failed with module upgrade \(2019.0.0\)

Using the `puppet-agent` module to upgrade Fedora 26 and 27 agents failed.

## Orchestrator errors for large task payloads \(2019.0.0\)

Previously, when running tasks that passed arguments larger than approximately 64KB, Orchestrator errors occurred. This was most likely to occur when using Bolt over the Puppet Communications Protocol to upload a file.

## Setting node group environment required Edit configuration data permission

To allow a user role to set a node group environment, users previously had to add the permission **Edit configuration data** in addition to **Set environment**. The permission **Set environment** alone is now enough to allow a user to change the environment.

