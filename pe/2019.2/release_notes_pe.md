---
author: Melissa Amos <melissa.amos@puppet.com\>
---

# PE release notes

These are the new features, enhancements, resolved issues, and deprecations in this version of PE.

## PE 2019.2.2

### Resolved issues

#### Orchestration service shuts down after code deploy

The initial code deploy after upgrade could cause the orchestration service to shut down if there were errors with the JRuby interpreter. Improved error handling now ensures that the orchestration service remains operable for Puppet run and task run functionality even if there are JRuby errors.

#### The `notice` function is now available for plans

The `notice` function did not work for the initial release of plans for PE. Now, `notice` is fully functional with plans.

## PE 2019.2.1

### Resolved issues



#### Upgrade failed if PuppetDB was offline

When upgrading to this version, installation failed during validation with an error about connecting to PuppetDB.

#### Upgrade could fail in environments with external PE-PostgreSQL

When you ran the installer in an environment with external PE-PostgreSQL, if the orchestrator database username was not `pe-orchestrator`, upgrade on the master failed.

#### Upgrade failed if IPv6 was disabled

If IPv6 was disabled on your system, upgrade failed with a Protocol family unavailable error. With this release, upgrade uses IPv4 by default. If you prefer to use IPv6, you can modify `"puppet_enterprise::ip_version": 6` in your `pe.conf` file before you upgrade.

## PE 2019.2.0

### New features

#### Manage network devices with Puppet Enterprise

You can add network devices to a PE deployment using the **Inventory** option on the console. This feature requires that you have installed modules for device transports in your production environment. By adding connections to those devices to your inventory, you can manage switches and firewalls, and run Puppet and tasks on them, just like other agentless nodes in your infrastructure. For more information on how to add devices and run jobs on them, see

-   [Add devices to the inventory](adding_and_removing_nodes_inventory.md#)
-   [Run a task on a node list](running_tasks_in_the_console.md#)
-   [Run Puppet on a node list](running_puppet_on_demand_in_the_console.md#)

    CAUTION:

    This initial implementation of network device configuration management is limited in how many device connections it can handle. Managing more than 100 devices can cause performance issues on the master. We plan to gather feedback from this release and improve the scaling and performance capabilities. In the meantime, avoid impacting master performance by limiting the total number of network device connections you make to 100.


#### Plans in PE

You can run plans in the PE console and on the command line. For more information, see [Running plans in PE](running_plans_in_pe.md).

#### Agent installation from the console

You can install Puppet agents on target nodes from the **Inventory** option on the console. For more information, see [Install agents from the console](installing_agents.md#).

#### View plan content

You can view plan details and information about plan jobs you have run using the `/plan_jobs` [endpoints](orchestrator_api_plan_jobs_endpoint.md#).

#### Added 'check node-connections endpoints

Added the endpoints `/ssh/check_node_connections` and `/winrm/check_node_connections` to `bolt-server` to support the`wait_until_available` feature in the console for plans. See the [developer docs](https://github.com/puppetlabs/bolt/commit/47b36d9649047645277ee3c5f471f1d97c588cab) for more information.



#### FIPS compliance

This version of Puppet Enterprise complies with Federal Information Processing Standard \(FIPS\) 140-2 standards and is fully operable on select FIPS platforms. For more information on FIPS-compatible PE, see [PE and FIPS compliance](fips_compliance.md).

### Enhancements

#### PostgreSQL 11 upgrade

PE version 2019.2 upgrades PostgreSQL to version 11. If you are upgrading to PE version 2019.2, carefully review the information on datastore migration in [PostgreSQL 11 upgrade in PE 2019.2](upgrading_pe.md#) before you begin. If you have an external PostgreSQL instance, you must upgrade it before you upgrade your master, compliers, and agents.

#### List node groups by name for a given node

Using an updated classification endpoint for the node classifier API v2, you can generate an array of the groups that a node was classified into and sort the list by group name and ID. Previously group lists were limited to ID.

```
"groups": [{"id": "9c0c7d07-a199-48b7-9999-3cdf7654e0bf",
              "name": "a group"},
             {"id": "96d1a058-225d-48e2-a1a8-80819d31751d",
              "name": "b group"}],

```

In addition to the new classifier endpoint, a new top-scope variable is available, `$pe_node_groups`, to use in your Puppet code. This variable contains the same content as the new groups value in the classifier, the names and group identifiers for the groups that were used to classify the nodes.

**Important:** `$pe_node_groups` cannot be interpolated when used as a classifier in class variable values.

For more information, see [POST /v2/classified/nodes/<name\>](post_v2_classified_nodes_name.md).

#### Specify alternate DNS names when regenerating certificates

When regenerating agent or master certificates, you can now pass an optional `dns_alt_names` parameter to the Bolt task.

#### Job export file contains new error messages column

You can now view full error messages for failed job runs on the job export file. The new column is called **Job node status detail**.

#### More RBAC control for plan permissions

This version implements `plan:run<planname>` and `plan:run:*` \(all plans\) to allow for more control over permissions in plans.

#### Additional settings can be configured in the console

The orchestrator now contains a new JRuby pool to facilitate plans in PE. New configuration options are now available as orchestrator settings to provide control over the JRuby pool.

You can now access the following settings in the orchestrator:

-   `jruby_borrow_timeout` - how long to wait for a JRuby instance before timing out
-   `jruby_compile_mode` - the compile mode that JRuby is in, can be 'jit' or 'off'
-   `jruby_max_active_instances` - the max number of concurrently running JRuby instances at any one time.
-   `jruby_max_requests_per_instance` - the max number of times a JRuby instance is re-used before it is destroyed and replaced by a new one.
-   `reserved_code_cache` - a JVM argument that has been exposed to help better configure the JVM arguments in orchestrator.

#### Execute Bolt actions for pxp-agent

Previously, running a command over PCP via pxp-agent required wrapping the command in a temporary task wrapper via `bolt_shim` because pxp-agent was not able to respond to any other types of PCP requests for Bolt actions.

Now, pxp-agents can respond to the following requests over PCP without formatting them as tasks:

-   command run
    -   Please note, the executable `/opt/puppetlabs/puppet/bin/task_wrapper` has moved to `/opt/puppetlabs/puppet/bin/execution_wrapper`. Anyone using this executable directly should update their paths accordingly. Its functionality remains the same as before -- this is only a name change to reflect that it is used for more than one type of Bolt action. Formerly, this was just `task run`, now it is `command run` as well.
-   `download file`
-   `run script`

#### CLI update for `puppet plan run`

This change modifies puppet-plan CLI to use the same flag names as puppet-task for options that are the same across both tools. It also modifies puppet-plan so that it is able to read configured values for the CA cert path, the token, and the orchestrator service URL from files created by pe-client-tools.

#### FIPS Bouncy Castle for Puppet Server

Puppet Server will now use the FIPS version Bouncy Castle when running on systems with FIPS enabled.

#### Support script version 3

PE version 2019.2 introduces version 3 of the Puppet Enterprise support script. By adding the optional `--v3` flag to the support script command, you can access new options that allow you to fine-tune the information you gather and send to the Support team. For more information, see [Puppet Enterprise support script](getting_support_for_pe.md#).

#### Architecture terminology changes

We've updated the terms we use to talk about PE reference architecture types, and are no longer using the term "monolithic."

|Configuration|Nodes|Former name|New name|
|-------------|-----|-----------|--------|
|All infrastructure components on the master.|Up to 4,000|Monolithic|**Standard**|
|All infrastructure components on the master, plus one or more compilers and a load balancer.|4,000 to 20,000|Monolithic with compilers|**Large**|
|All infrastructure components on the master, plus one or more compilers and a load balancer, plus a separate node which hosts the PE-PostgreSQL instance.|More than 20,000|Monolithic with compilers and standalone PE-PostgreSQL|**Extra large**|

Please note that a Standard Support Plan, one of the two levels of commercial support available from Puppet, is not restricted to PE instances using a standard reference architecture. "Standard" in this case refers to the support level, not the size of the PE installation.

#### Platform support

This version adds support for these platforms.

**Master**

-   FIPS 140-2 compliant Enterprise Linux 7


**Agent**

-   Debian Buster \(10\)

### Deprecations and removals

#### Split and large environment installations

Split and large environment installations, where the master, console, and PuppetDB were installed on separate nodes, are no longer supported, and you can't upgrade to this version with a split install. For instructions on migrating from a split installation to a standard \(formerly called monolithic\) installation, see [Migrate from a split to a standard installation](https://puppet.com/docs/pe/2019.1/upgrading_pe.html#migrate-split-mono).

#### Web installation

The web-based method for installing infrastructure components has been removed. For a simplified installation, use the [express install](installing_pe.md#) method instead.

#### Console admin password reset script

The Ruby script for resetting the console administrator password has been removed in favor of the new `puppet infrastructure console_password` command.

#### Use of `pe_ini_subsettings` to manage Java arguments

Java arguments on all Trapperkeeper services are now fully managed by the `puppet_enterprise` module, and use of `pe_ini_subsettings` to manage Java arguments is no longer supported. If you add Java arguments to the `java_args` environment variable using `pe_ini_subsettings`, the arguments will be removed during the next Puppet run.

#### Deprecated platforms

These platforms are deprecated in this release and will not be supported in the next major version of PE.

**Master**

-   Enterprise Linux 6
-   Ubuntu 16.04

#### Platforms reaching end of support

These platforms have reached end-of-life and are no longer supported.

**Agent**

-   Ubuntu 14.04

### Resolved issues



#### New Tasks fails to show status in the console

Occasionally when running a new task, the target node did not appear on the **Jobs** page with a status until you refreshed the console. This was the result of a time sync issue between the node event and the local orchestrator time, and has been fixed.

#### Package versions were not reset correctly after failed upgrade

If an agent run was in progress as an upgrade began, and consequently the installer failed because it could not acquire an agent lock, the installer did not roll back the relevant packages to the pre-upgrade PE version.

#### Replicas tried to query PuppetDB on the primary master

In high availability installations, the replica was incorrectly configured to first send queries to the PuppetDB service on the primary master. The failover list has been corrected so that the replica now queries its own PuppetDB service first.

#### Rerunning the installer created the All Environments node group

If the installer was run for a second time due an issue such as a failed upgrade or a faulty agent lock, the All Environments node group was mistakenly created for the installation. This issue has been resolved, and the All Environments node group is only created for new installations.

#### `puppet infrastructure run` plans caused Puppet agent service settings to be ignored

In some cases, plans used in `puppet infrastructure run` commands forced the Puppet agent service to run after the plan was complete, even if you had previously disabled the service. The impacted plans now reset the Puppet agent service to the state it was in before the plan was run.

#### Certificate backup directories could be overwritten

When a certificate regeneration command was run multiple times, certificate backup directories could be unintentionally overwritten. To solve this issue, certificate backup directories are now uniquely named using a time stamp.

#### Setting node group environment required `Edit configuration data` permission

To allow a user role to set a node group environment, users had to add the permission **Edit configuration data** in addition to **Set environment**. Now, the permission **Set environment** alone is enough to allow a user to change the environment.

#### Upgrade attempts failed when a Puppet run was in progress

If a Puppet run began while the installer was attempting to upgrade PE, conflicts and failures occurred. The installer now checks for Puppet runs before beginning an upgrade, and stops the upgrade if one is in progress.

#### Analytics can report on modules without metadata

Previously, modules without a `metadata.json` file installed on the Master would break analytical submissions and leave a schema error in the log. Now, analytics will report correctly on installed modules without metadata.

#### Run a task on agentless node using ed25519 key

You can now use the ed25519 SSH keys to run tasks against agentless SSH nodes.

