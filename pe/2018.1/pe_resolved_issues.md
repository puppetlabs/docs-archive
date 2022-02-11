# Resolved issues

These are the issues resolved in PE 2018.1.x.

## Restarting orchestration-services caused pcp-brokers to become unresponsive \(2018.1.15\)

When pe-orchestration-services restarts on PE masters, the pcp-brokers connected to master no longer fail when attempting to reconnect to the master after it returns.

## Metrics API could leak sensitive information \(2018.1.15\)

Puppet Server and PuppetDB could leak sensitive information such as hostnames through the metrics API. This version disables the `/metrics/v1` endpoints by default and restricts access to the `/metrics/v2` endpoints to localhost. \([CVE-2020-7943](https://puppet.com/security/cve/CVE-2020-7943/)\)

## Default for `strict_hostname_checking` changed to `true` \(2018.1.13\)

The default setting for `strict_hostname_checking` in PE was changed to `true` to resolve a security issue. PE users who are not upgrading should manually set `strict_hostname_checking` to `true` to ensure secure behavior. You must also specify the fully qualified domain name of the host when referring to nodes; partial hostname matches, for example `node /^foo/` are no longer supported.

## Class parameter selection in Firefox had inconsistent behaviors \(2018.1.13\)

In the current versions of Firefox, some odd behavior appeared when editing class parameters. The class parameter selection would impact other selections in the class in unexpected ways. As a result, the ability to edit the parameter was removed. You can still edit the parameter value, but cannot choose a different parameter. This resolves the issue in Firefox, simplifies the interaction, and makes the behavior consistent with other pages.

## PE ports were vulnerable to LOGJAM \(2018.1.13\)

PE ports were vulnerable to LOGJAM because they used common, shared Diffident-Hellman primes. The default Diffident-Hellman ephemeral key size has been increased to 2048 for all JVM PE services, like Puppetserver, PuppetDB, etc.

## Mismatch between classifier classification and matching nodes for regexp rules \(2018.1.12\)

PuppetDB’s regular expression matching has surprising behaviors for structured fact value comparisons. For example, the structured fact ‘os’ is a rule that matches \`\["~", "os", ":"\]\`. PuppetDB would unintentionally match every node that has the ‘os” structured fact because the regular expression is applied to the JSON encoded version of the fact value.

The classifier does not use PuppetDB for determining classification and regular expressions in the classifier rules syntax only support direct value comparisons for string types.

This has caused issues in the console where the node list and counts for the "matching nodes" display sometimes indicated that nodes were matching even though the classifier would not consider them matching.

Now, the same criteria are applied to the displays and counts that the classifier uses. The output of the classifier’s rule translation endpoints will also make queries that match the classifier behavior.

**Note:** This fix does not change the way nodes are classified, it only corrects how the GUI displays matching nodes.

## Console was inaccessible on macOS Catalina using default certificates \(2018.1.12\)

Enhanced security requirements in macOS Catalina prevented accessing the console using the default certificate generated during installation.

## `puppet infrastructure run` commands could fail if the agent was run with cron \(2018.1.12\)

`puppet infrastructure run` commands, such as those used for certain installation, upgrade, and certificate management tasks, could fail if the Puppet agent was run with cron. The failure occurred if the command conflicted with a Puppet run.

## Replicas tried to query PuppetDB on the primary master \(2018.1.11\)

In high availability installations, the replica was incorrectly configured to first send queries to the PuppetDB service on the primary master. The failover list has been corrected so that the replica now queries its own PuppetDB service first.

## Puppet run failures after a split installation with multiple PuppetDB instances was migrated to a monolithic installation \(2018.1.11\)

If your split PE installation architecture included multiple standalone PuppetDB instances, the `puppet infrastructure run migrate_split_to_mono` command could not create a PuppetDB instance in the new monolithic installation, and subsequent Puppet runs failed. The `puppet infrastructure run migrate_split_to_mono` command now exits with an error message if multiple PuppetDB instances are present in your split installation.

## Rerunning the installer created the All Environments node group \(2018.1.11\)

If the installer was run for a second time due an issue such as a failed upgrade or a faulty agent lock, the All Environments node group was mistakenly created for the installation. This issue has been resolved, and the All Environments node group is only created for new installations.

## Console output was shown when installing in quiet mode \(2018.1.11\)

When running the installer in text mode, adding the `-q` option did not successfully activate quiet mode, and the installation process was logged in the console.

## `puppet infra run` plans caused Puppet agent service settings to be ignored \(2018.1.11\)

In some cases, plans used in `puppet infrastructure run` commands forced the Puppet agent service to run after the plan was complete, even if you had previously disabled the service. The impacted plans now reset the Puppet agent service to the state it was in before the plan was run.

## Package versions were not reset correctly after failed upgrade \(2018.1.11\)

If an agent run was in progress as an upgrade began, and consequently the installer failed because it could not acquire an agent lock, the installer did not roll back the relevant packages to the pre-upgrade PE version.

## Upgrade attempts failed when a Puppet run was in progress \(2018.1.11\)

If a Puppet run began while the installer was attempting to upgrade PE, conflicts and failures occurred. The installer now checks for Puppet runs before beginning an upgrade, and stops the upgrade if a run is in progress.

## Certificate backup directories could be overwritten \(2018.1.11\)

When a certificate regeneration command was run multiple times, certificate backup directories could be unintentionally overwritten. To solve this issue, certificate backup directories are now uniquely named using a time stamp.

## Setting node group environment required Edit configuration data permission \(2018.1.10\)

To allow a user role to set a node group environment, users previously had to add the permission **Edit configuration data** in addition to **Set environment**. The permission **Set environment** alone is now enough to allow a user to change the environment.

## Issues discarding changes to RBAC member groups \(2018.1.9\)

In previous versions of PE, if you made changes to member groups for a selected user role and later clicked **Discard changes** instead of **Commit changes**, the changes were not cleared properly. This has been fixed.

## Replicas did not receive new or updated packages \(2018.1.9\)

When new packages were introduced on the master, whether as the result of a PE upgrade or the introduction of new agents,existing replicas did not receive the new packages.

## When removing replicas, empty parameters were added to the PE Agent group \(2018.1.9\)

When you run `puppet infrastructure forget` on a high availability deployment that uses load-balanced compile masters, `server_list` and `pcp_broker_list` parameters are no longer added to the `puppet_enterprise::profile::agent` class with their values set to empty arrays.

## Upgrade failures caused the `pe-installer` package to uninstall \(2018.1.9\)

If any failure occurred during your upgrade of PE, the `pe-installer` package was automatically uninstalled.

## Reinitializing a replica after upgrade failed \(2018.1.9\)

When upgrading a replica, the reinitialize command hung and failed after five minutes depending on the order of services in your `/etc/puppetlabs/client-tools/services.conf` file.

## Enabling a new replica using a previous master failed with autosign enabled \(2018.1.9\)

The `puppet infrastructure run enable_ha_failover` command, which lets you enable a failed master as a new replica, includes a step for signing the node's certificate. With autosign enabled, an unsigned certificate couldn't be found, and the command errored out.

## Regenerating master certificates failed if the command conflicted with automatic backups \(2018.1.9\)

The `puppet infrastructure run regenerate_master_certificate` command failed if it ran at the same time as automatic configuration backups, triggering an error about pre-existing key files.

## Regenerating agent certificates failed with autosign enabled \(2018.1.9\)

The `puppet infrastructure run regenerate_agent_certificate` command includes a step for signing the node's certificate. With autosign enabled, an unsigned certificate couldn't be found, and the command errored out.

## Backup failed with File changed as we read it error \(2018.1.9\)

If file sync tried to change files while the backup command was archiving files, the backup command errored and failed.

## Restore failed if `/tmp` folder was too small \(2018.1.9\)

When restoring a master using `puppet backup restore`, the `/tmp` folder was always used as the temporary location for unpacking PostgreSQL dumps. If `/tmp` didn't have enough space to hold these dumps, the restore failed. Additionally, attempting to use a different temp directory by setting the `TMPDIR` environment variable did not work correctly.

## Restore reset master DNS altnames \(2018.1.9\)

When restoring a master using `puppet backup restore`, `pe_install::puppet_master_dnsaltnames` was reset as an array with only the certname of the restore host.

## Usernames appear as Base64 strings \(2018.1.9\)

Under some circumstances when using an LDAP server, usernames that contained extended characters would incorrectly appear as a Base64 encoded string. For example, a username spelled with a German umlaut, Schröder, would appear in the string format **U2NocsO2ZGVyDQo=**. After upgrading to PE 2018.1.9, users impacted by this issue must log out and log back in to see their usernames correctly spelled.

## Puppet file permissions on Windows were modified with every run \(2018.1.8\)

Changes to how Puppet handled system permissions caused permissions for Windows file resources to be rewritten with each run.

Puppet now treats owner and group on file resources as in-sync in either of these scenarios:

-   owner and group are not set in the resource

-   owner and/or group are set to the system user on the running node **and** the system user is set to full control


You can specifically configure the system user to less than full control by setting the owner and/or group parameters to`SYSTEM` in the file resource. In this case, Puppet emits a warning, because setting `SYSTEM` to less than full control might have unintended consequences, but it does not modify the permissions.

## White space and non-UTF-8 characters in package names and versions triggered invalid byte sequence error \(2018.1.8\)

When collecting package names and versions, the Windows Package Inspector replaced white spaces and non-UTF-8 characters with "�", causing Puppet runs to fail or submit data that PuppetDB rejected.

## Restoring to a former agent node with a signed certificate in the master's `ssl/ca/signed` directory failed \(2018.1.8\)

If you attempted to back up data to a former agent node whose node certificate still existed in the `/etc/puppetlabs/puppet/ssl/ca/signed` directory that was part of the backup tarball, the restore failed when it attempted to regenerate a new certificate for the master.

## Upgrade could trigger non-blocking errors \(2018.1.8\)

Upgrade triggered the error -lt: unary operator expected when verification of the PostgreSQL version number encountered a "."

## After upgrade, agent runs triggered a cyclical dependency \(2018.1.8\)

After upgrade, a Puppet run could trigger a cyclical dependency if any of the folders listed below were managed with a file resource on a PE infrastructure node.

-   RHEL — /etc/yum.repos.d

-   Ubuntu — /etc/apt/sources.list.d

-   SUSE Linux Enterprise Server — /etc/zypp/repos.d


You can now manage YUM and APT repositories on infrastructure nodes without encountering cyclical dependency errors from `puppet_enterprise::repo::config`.

## Classifier inconsistently applies rules to escaped numerical variables \(2018.1.8\)

Previously, the node classifier service API inconsistently applied escaped numerical variables to string parameters in class declarations. Where these variables were entered by themselves \(`"\\$1"`\) they were correctly processed. Where these variables were nested in key-value pairs \(`{"key" : "a value \\$1"}`\) they were incorrectly processed as object values. Escaped numerical variables are now processed consistently and correctly.

## Script installation failed on Solaris 11.4 agents \(2018.1.7\)

Due to a change in operating system identification for Solaris 11.4, agent installation using the install script failed with an error indicating the installation method wasn't supported.

## Master and replica weren't pinned to appropriate node groups after promotion \(2018.1.7\)

When a replica was promoted, the retired master remained pinned to the `PE Database` node group, and the newly promoted master was *not* pinned to the group. Consequently, the database was not automatically managed after you promoted a replica.

## Package updates failed on promoted replicas \(2018.1.7\)

On promoted HA replicas, the managed package repository configuration contains out-of-date references to the old primary master. This causes package updates, like `yum update`, to fail because the repository is pointing to the old master. In general, the packaging configuration artifacts for PE infrastructure are now named `puppet_enterprise` across all platforms. The temporary artifacts created by the `pe_repo` module for agent installation are named `pe_repo`.

## Permissions weren't set correctly on global Hiera file \(2018.1.7\)

On systems with a umask other than 000, the installation process didn't correctly set permissions on the global Hiera file at `/etc/puppetlabs/puppet/global_hiera.yaml`.

## Specifying `gc_interval` as an integer caused agent runs to fail \(2018.1.7\)

If the`puppet_enterprise::profile::puppetdb::gc_interval` parameter was set as an integer, agent runs failed. You can now specify `gc_interval` as either an integer or a string.

## Puppet runs generated an autosign warning \(2018.1.5\)

During Puppet agent runs, a warning appeared indicating that autosign was deprecated. We un-deprecated autosign and this warning no longer appears. For more information about autosigning, see [Autosigning certificate requests](https://puppet.com/docs/puppet/5.5/ssl_autosign.html).

## Fedora 26 and 27 agent upgrade failed with module upgrade \(2018.1.5\)

Using the `puppet-agent` module to upgrade Fedora 26 and 27 agents fails.

## Orchestrator errors for large task payloads \(2018.1.5\)

Previously, when running tasks that passed arguments larger than approximately 64KB, Orchestrator errors occurred. This was most likely to occur when using Bolt over the Puppet Communications Protocol to upload a file.

## Deploying additional code or environments blocked Puppet Server startup \(2018.1.5\)

Using Code Manager to deploy additional code and environments increased the amount of time it took to start, restart, or reload the pe-puppetserver service, to the point that these operations could time out. The increase in startup time was caused by a diagnostic that checked every file in every environment and logged any modifications. This diagnostic has been removed.

## Running `r10k deploy` on Bionic triggered a Ruby bug \(2018.1.5\)

Running either r10k 2.6 or 3.0 with the shellgit provider on Bionic could trigger a Ruby bug when running `r10k deploy`. This bug was more likely the more environments you deploy.

## Slow response times when filtering nodes \(2018.1.5\)

Previously, when you filtered nodes based on fact values \(a feature available from the Overview and Classification pages\) in an environment with a large number of nodes, it would result in slow response times and, in some cases, cause timeout errors. Now when you filter nodes, a limited number of concurrent node queries is run, which improves performance.

## Enumerated task parameter values not available in drop-down lists \(2018.1.5\)

When a single task parameter value that used the enumerated data type contained a hyphen \(-\), none of the set values appeared in drop-down lists. For example, these set values `Enum[install, status-only, uninstall]` would not appear because of the value **status-only**. This has been fixed.

## Requests to the `/status/v1/simple` API endpoint were denied \(2018.1.4\)

Requests to the Puppet Server`/status/v1/simple` endpoint are no longer denied.

## Code Manager shut down Puppet Server if a file path contained Java format specifiers \(2018.1.4\)

When a file name containing the character `%` was committed to a control repository or module, File Sync interpreted it as a  [Java format specifier](https://docs.oracle.com/javase/8/docs/api/java/util/Formatter.html#syntax). When this interpretation failed, Code Manager initiated a shutdown of the Puppet Server.

## Web-based installation failed MCollective validation \(2018.1.4\)

Web-based installation still checked for, and failed, MCollective validation even though MCollective was deprecated and no longer installed on new masters beginning in 2018.1.0.

## Dynamic node matching using either start- or end-of-line regex erroneously showed no matches \(2018.1.3\)

When using a start-of-line anchor \(^\) or an end-of-line anchor \($\) to dynamically match nodes in the console, no matches were found, even if matches existed. For example, if you had a node named jupiter, using a regex match \(~\) for `iter` shows 1 match, but using a regex match `iter$` showed no matches.

## Puppet runs were slow when the `puppetlabs-puppetserver_gem` was installed on the master \(2018.1.3\)

When the `puppetlabs-puppetserver_gem` was installed on the master, Puppet runs became markedly slower, with a long pause before the catalogue was applied.

## MCollective was installed on new high availability replicas \(2018.1.3\)

MCollective was installed on new replicas even though the feature was deprecated and no longer installed on new masters beginning in 2018.1.0.

## Enabling package data collection affected MCollective \(2018.1.3\)

When enabling package data collection to view a node's current package inventory, MCollective fact filtering would break. This has been fixed.

## Certificate API requests deadlocked high availability replicas \(2018.1.3\)

Submitting a CA API request to a replica caused the replica to run out of free worker threads and deadlock.

## Critical corruptive permissions issue with Windows installer for Puppet agents 1.10.13, 5.3.7, and 5.5.2 \(2018.1.2\)

Due to a critical issue with the Windows installer, Puppet agents 1.10.13, 5.3.7, and 5.5.2 could inadvertently change permissions on files across a Windows node's filesystem during installation or upgrade.

## Upgrades failed on compile masters set to use cached catalogs \(2018.1.2\)

If `use_cached_catalogs` was set to `true` on a compile master, upgrade failed.

## `puppet generate types` failed on the `pe_java_ks` module \(2018.1.2\)

Running `puppet generate types` on the `pe_java_ks` module resulted in an error.

## PostgreSQL temporary file logging was excessive \(2018.1.2\)

PE-PostgreSQL logged an excess of temporary files created by a new PuppetDB query. With this release, temporary files are logged only if they're larger than the size specified by the PostgreSQL`work_mem` setting.

## Options missing from RBAC external directory page \(2018.1.2\)

Security protocols, hostname and wildcard validation, and other configuration options have been added to the External directory page in the console. Previously, you were required to configure these options through the directory service \(`ds`\) API endpoints.

## Parameters missing from PXP agent class \(2018.1.2\)

The parameters `task-download-connect-timeout` and `task-download-timeout` have been added to the class `puppet_enterprise::pxp_agent`. For each parameter, set an integer that specifies how much time should pass before the connection or download times out.

## Invalid URL appeared in analytics web service logging \(2018.1.2\)

The invalid URL `http://null:null/analytics/v1` has been removed from the web service logging output message.

## Slow response times for list of fact values \(2018.1.2\)

The drop-down list of facts, available when you write rules to assign nodes to a group, has been limited to 500 entries in order to improve response time. In previous releases, installations with large numbers of custom facts sometimes experienced slow response times to view this list.

## Slow response times for node classification \(2018.1.2\)

An internal cache of the classes has been added to improve the response times from the classifier when creating and updating node groups. In previous releases, installations with large numbers of environments sometimes experienced slow response times.

## Unquoted hyphens not permitted in identifiers \(2018.1.2\)

You can use hyphens without quotation marks when you enter identifiers in PE. For example, the following is valid task metadata: `{"parameters": {"action": {"type": "Enum[com-start]"}}}`. Previously, such an identifier would cause errors.

## Disallowed  Code Manager proxy setting for the Forge

This release fixes a regression that disallowed the proxy key for the Code Manager`forge_settings` parameter.

## Tasks that produce output with null bytes remain "in progress" after failing \(2018.1\)

Tasks that produce output with null bytes result in node failures rather than hanging the entire job run.

## pcore-java uses java.util.regex instead of jruby/joni \(2018.1\)

Previously, the type checking for tasks using data types involving regular expressions could be exposed to subtle differences between the regexp implementations in Ruby and the implementations in JVM. The type system support running on Orchestrator has been updated to use the same regular expression implementation as JRuby. 

**Note:** This change might result in patterns accepting input that they did not before. In particular, `/\w+/` matches partial strings; to restore the original behavior use anchors, for example, `/\A\w+\z/`.

## Errors when creating node group UUIDs through the API \(2018.1\)

Previously, if you created a group with a self-generated UUID using the PUT /v1/groups/:id end point, the classifier would accept some UUIDs that the console considered invalid. As a result the console would generate the error, "Error retrieving group hierarchy: The group id specified is invalid." The regular expression used to validate these identifiers in the console has been made less restrictive to match the classifier behavior. It is: `[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}`. 

