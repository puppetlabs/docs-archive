# Resolved issues

These are the issues resolved in PE 2017.3.x.

## Certificate API requests deadlocked high availability replicas \(2017.3.9\)

Submitting a CA API request to a replica caused the replica to run out of free worker threads and deadlock.

## Critical corruptive permissions issue with Windows installer for Puppet agents 1.10.13, 5.3.7, and 5.5.2 \(2017.3.8\)

Due to a critical issue with the Windows installer, Puppet agents 1.10.13, 5.3.7, and 5.5.2 could inadvertently change permissions on files across a Windows node's filesystem during installation or upgrade.

## Upgrades failed on compile masters set to use cached catalogs \(2017.3.8\)

If `use_cached_catalogs` was set to `true` on a compile master, upgrade failed.

## Slow response times for node classification \(2017.3.8\)

An internal cache of the classes has been added to improve the response times from the classifier when creating and updating node groups. In previous releases of Puppet Enterprise, installations with large numbers of environments sometimes experienced slow response times.

## Escape characters not applied to query strings before display \(2017.3.6\)

Escape characters are applied to query strings before they are displayed. In previous versions of PE \(2017.3.x\), queries used for running tasks and jobs were not properly sanitized and tags were rendered.

## Errors when creating node group UUIDs through the API \(2017.3.6\)

Previously, if you created a group with a self-generated UUID using the PUT /v1/groups/:id end point, the classifier would accept some UUIDs that the console considered invalid. As a result the console would generate the error, "Error retrieving group hierarchy: The group id specified is invalid." The regular expression used to validate these identifiers in the console has been made less restrictive to match the classifier behavior. It is: `[0-9a-f]{8}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{4}-[0-9a-f]{12}`.

## Configuration changes logged as deletions in node group activity \(2017.3.6\)

Configuration changes made to node groups are correctly recorded in the activity history. In previous versions of PE, the Classifier Service would incorrectly report configuration data entries as deletions.

## `apt-get update` hung on promoted replicas running Ubuntu 14.04 \(2017.3.6\)

After promoting a replica running Ubuntu 14.04, the file `/etc/apt/sources.list.d/puppet-enterprise-installer.list` caused `apt-get update` to hang.

## Japanese translations didn't display during installation \(2017.3.5\)

When using the installer on an infrastructure node with locale set to Japanese, the installer continued to display in English.

## Enterprise Linux 7 AArch64 upgrades using the puppet\_agent module failed \(2017.3.5\)

Enterprise Linux 7 AArch64 agent upgrades using the puppet\_agent module failed to upgrade the agent on the target system.

## Task results containing an `_error` key did not result in failure \(2017.3.4\)

Tasks that return an `{{_error}}` property as part of a JSON result now fail, similar to if they exited non-zero. If the `{{_error}}` property has an object like `{ "msg": "an error message", }` as a value, the message will now be displayed in the console and CLI.



## PostgreSQL upgrade broke replication \(2017.3.4\)

With high availability enabled, upgrading to PE 2017.3 or later caused a `pglogical` failure which prevented data from synching to the replica.

## Upgrade failed on installations with external PostgreSQL \(2017.3.4\)

When upgrading a split installation with an external PostgreSQL node, the installation failed and reverted all package upgrades because the PostgreSQL version couldn't be validated.

## Agent jobs and tasks failed after upgrading an HA installation with additional compile masters \(2017.3.4\)

When upgrading an HA installation with additional compile masters, the pcp-broker service was disabled on compile masters. Agent jobs and tasks, which depend on the pcp-broker service, failed unless you completed a workaround.

## HA provisioning failed \(2017.3.4\)

When provisioning a replica, the first orchestrator job started by the `puppet infrastructure provision replica` command sometimes failed, with console services, orchestrator, and PuppetDB reported as down by `puppet infrastructure status`. This issue was seen most often on Ubuntu 14.04.

## Broken Puppet package providers caused PostgreSQL upgrade to fail \(2017.3.4\)

Upgrading PostgreSQL depended on info provided by the `pe_postgresql_info` custom fact in the `pe_install` module. If that fact failed to resolve for any reason, the upgrade process installed PostgreSQL 9.6, but skipped migrating data.

## Razor upgrade to PE 2017.3.2 and earlier failed \(2017.3.4\)

An issue with the PostgreSQL upgrade in PE 2017.3 caused upgrade of Razor server nodes to fail. The failure resulted in an error message similar to this:

```
Notice: /Stage[main]/Pe_install::Upgrade::Postgres/Exec[migrate to new postgres]/returns: Failure, exiting
```

## Razor couldn't be installed or upgraded to 2017.3.2 or earlier on a node whose OS differed from the master \(2017.3.4\)

Due to a bug in the `pe_razor` module, you couldn't install or upgrade the Razor server on a node whose operating system was different from the master.

## External directory service searched nested groups by default \(2017.3.3\) 

In PE 2017.3.3, the external directory service no longer searches nested groups by default; the directory service connection setting `search_nested_groups` has a default value of `false`. To search for groups that are members of an external directory group, you must change this value to `true`.

In PE 2015.3 and earlier versions, if you connected with an external directory service to work with users and groups already established in your directory, the default behavior was to search nested groups. When upgrading from one of these earlier versions, this default setting is preserved. For organizations with a large number of nested group memberships, this can slow performance. To avoid performance issues, change the `search_nested_groups` field to `false` for a more shallow search in which RBAC only searches the groups it has been configured to use for user roles.

## Solaris and AIX upgrades using the puppet\_agent module failed \(2017.3.2\)

Solaris and AIX upgrades using the puppet\_agent module left the agent uninstalled on the target system. This issue is resolved in version 1.4.2 of the puppet\_agent module.

## Database replication didn't work after forgetting an HA replica \(2017.3.2\)

Forgetting a replica in PE 2017.2 and earlier caused console services database replication to fail after upgrade and prevented changes made in the console from synching to the replica. These issues were caused by leftover pglogical event triggers.

## Job related translation improvements \(2017.3.2\)

In a couple of instances, text from a job run was not translated. These issues have been fixed:

-   If you ran a job with the command, `puppet job run --query 'nodes {}',` the response contained untranslated text.

-   If you attempted to use the command, `puppet job show <job-id>` in the Japanese version of PE, the text, `COMPLETED RUNS`was not translated.

-   If you attempted to use `puppet job show` in the Japanese locale, the column headers were not translated.




## The orchestrator failed to identify that nodes have completed after compile master restart \(2017.3.2\)

If a compile master was restarted during a job, nodes were left in the "In progress" state. This is fixed.



## `POST` request to orchestrator/v1/inventory without a body/payload returned 200 \(2017.3.2\)

This `POST` request returned a 200 code, instead of the status code 400 with a message stating that the `POST` on this endpoint required a body/payload. This is now fixed.



## Database migration during upgrades could fail to finish \(2017.3.2\)

When upgrading to PE 2017.3.1, PuppetDB database migration could silently fail before finishing with a logged `PSQLException: ERROR: could not create unique index "fact_values_value_hash_key" Detail: Key (value_hash)=(\x2be88ca4242c76e8253ac62474851065032d6833) is duplicated.` error. This is fixed.

If this affected a PE master upgraded to 2017.3.1, normalize null values by running the PSQL command `UPDATE facts SET value=null WHERE value_type_id=4 AND value='null';`, then run `puppet infrastructure configure`.

## Database replication didn't work after forgetting an HA replica \(2017.3.2\)

Forgetting a replica in PE 2017.2 and earlier caused console services database replication to fail after upgrade and prevented changes made in the console from synching to the replica. These issues were caused by leftover pglogical event triggers.

## At large scale, node group pages with lots of rules failed to load \(2017.3.1\)

In implementations with node groups that are deeply nested and contain many rules, the displayed node counts for existing rules in a node group could cause performance issues. The display has been changed to only display node counts for rules that are newly added to a group by default. In addition, now the node count can be displayed on demand. 



## Enum values that resolve to non-string JSON types when running tasks in the console parsed incorrectly \(2017.3.1\)

This issues was fixed by ensuring that values contained in an enum are treated as a string.



## Resource based tasks needed 'name' parameter \(2017.3.1\)

The `package` and `service` task modules had a parameter for `package=` and `service=`; however, this changed to `name=`.

This change applies to the following tasks:

-   `puppetlabs-package`
-   `puppetlabs-service`

To stay up-to-date, make sure you update your production environment with the latest versions of the `package` and `service` modules from the Forge.



## Invalid module metadata corrected \(2017.3.1\)

Modules with tasks included in 2017.3 contained metadata that incorrectly indicated that the modules supported no-operation functionality \(also known as "no-op" mode\). The modules do not support no-op and attempting to test changes with `--noop` does not work.



## Module deprecation check caused increase in deployment time \(2017.3.0\)

In r10k versions 2.5.3 and 2.5.4, a module deprecation check was performed on every redeploy, causing a significant increase in deployment time. This issue has been resolved in versions 2.5.5 and later.

## Nodes with no reports generated console errors on report retrieval \(2017.3.0\)

If you clicked a node's report list, and the node didn't have any reports stored in PuppetDB, the console returned an error that it couldn't retrieve the report.

## Orchestrator job show command localizes timestamp \(2017.3.0\)

Previously, the `puppet job show` command did not localize the timestamp.

## Agent packages for AIX, Solaris, Mac OS X, and Windows weren't synched correctly to compile masters \(2017.3.0\)

Applying any of the `pe_repo::platform::aix_*` classes on a compile master that couldn't reach `pm.puppetlabs.com` resulted in a failure.

## Error launching MCollective after installing Puppet agents on IBM POWER8 \(2017.3.0\)

Puppet agents running RHEL 7 or Ubuntu 16.04 on IBM POWER8 hardware did not use Ruby gems correctly, preventing the MCollective service from starting. 



## PE ActiveMQ cannot start after a certname change \(2017.3.0\)

Changing the hostname and certificate of a node running `pe-activemq` would result in duplicated broker configuration blocks in `activemq.xml`that would prevent the service from starting.



## Puppet Server does not track metrics on the requests it makes to the classifier \(2017.3.0\)

Puppet Server now tracks metrics on the requests it makes to the node classifier.



## Windows Puppet agent support for Data Execution Prevention \(DEP\) \(2017.3.0\)

The Windows Puppet components were built without NXCOMPAT to explicitly specify an executable as incompatible with data execution prevention. This is fixed. Puppet agent 1.10.7 includes security robustness measures that enable data execution prevention \( /NXCOMPAT\) and address space layout randomization \( /DYNAMICBASE\) in the Windows version of the agent binaries.



