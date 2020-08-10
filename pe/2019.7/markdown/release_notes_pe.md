---
author: Melissa Amos <melissa.amos@puppet.com\>
---

# PE release notes

These are the new features, enhancements, resolved issues, and deprecations in this version of PE.

## PE 2019.7

### New features

#### PuppetDB on compilers

To improve scalability in large and extra-large installations, Puppet Server and PuppetDB services are now installed by default on new compilers. This change reduces bottlenecks and enables easier scaling, because your PuppetDB capacity is always matched to your server capacity.

New compilers that you install with the `puppet infrastructure provision compiler` command are configured with Puppet Server and PuppetDB services. After upgrading to this or later versions, you can convert existing compilers with the `puppet infrastructure run convert_legacy_compiler` command. You must open port 5432 from compilers to your master before converting compilers.

For best performance, follow these recommendations:

-   Avoid using a mix of new- and legacy-style compilers.
-   After adding new compilers or converting existing compilers, run `puppet infrastructure tune` on your master and adjust tuning for compilers as needed.



### Enhancements

#### Replica provisioning process and speed improvements

This release speeds replica provisioning up to 3600 percent by modifying the method for synching PuppetDB. Additionally, you can now enable a replica at the same time that you provision by using the `--enable` flag with `puppet infrastructure provision replica`.

#### Console navigation and workflow improvements

This release includes several console enhancements that provide a more intuitive navigational experience.

-   Renamed pages

    The **Inspect** section was renamed to **Enforcement**.

    The **Run** section was renamed to **Orchestration**.

    The **Overview** page was renamed to **Status**.

-   New task and plan workflow

    The **Task** and **Plan** pages now route to their own details page for status information about scheduled, running, or previously run tasks and plans. This change allows you to run a task or plan and view status information about a task or plan run without leaving that section of the console.

-   Run a task or plan on a node

    You can now run Puppet or a task on a single node from the node details page.

-   New Puppet run workflow

    Puppet runs no longer have a dedicated landing page in the sidebar. Instead, select **Run puppet** on the **Status**, **Reports**, and **Jobs** pages. The **Jobs** page is now reserved exclusively for Puppet run results.


#### Easier and more reliable upgrades

This version includes multiple usability and reliability improvements for upgrades.

-   The `puppet infrastructure upgrade compiler` command lets you upgrade one, some, or all of your compilers from your master with a single command.
-   The `puppet infrastructure upgrade replica` command lets you upgrade your replica from your master.
-   The PuppetDB subcommand, `delete-reports`, speeds upgrade by stopping PuppetDB and deleting all reports and their resource events. You can also now use the console to set `resource-events-ttl`, which determines how long PuppetDB stores a report's resource events.

#### Updates to default settings

-   Authentication token lifetime

    Tokens now have a default authentication lifetime of one hour, instead of the previous lifetime of five minutes. This increase accommodates longer-running `puppet infrastructure run` commands that use authentication tokens.

-   Signing of certificates with authorization extensions

    The Puppet CA is now configured to allow signing certificates with authorization extensions by default. This change enables easier setup of new compilers. Signing certificates that have authorization extensions is still disabled in the console, however the `puppetserver ca` CLI displays authorization extensions in its list output, providing all relevant details about certificates you're signing.

-   Windows bulk plug-in sync

    For Windows agents, the agent install script automatically downloads a tarball of plug-ins from the master before the agent runs for the first time. Previous releases added optional bulk plug-in sync for Windows agents, with the setting disabled by default. This release updates the default to enabled \(`pe_repo::enable_windows_bulk_pluginsync: true`\).


#### Plans are now compatible with Bolt 2.0

PE now requires Bolt 2.0 style plans. Some notable changes include:

-   JSON output now has `target_count` instead of `node_count`.
-   JSON result objects now have `target` and `value` keys instead of `node` and `result`.
-   `add_facts(s)` now returns the Target passed to it. Previously, the function returned the Target's set of facts.
-   `Target.new` no longer accepts an options key. Both `Target.new("options" => ...)` and `Target.new($uri, "options" => ...)` are no longer allowed. `Target.new` now accepts *either* a string argument, which is the URI or a hash argument shaped like a target, in the inventory file.
-   `run_plan(plan::name, $targets)` will fail if the plan has both a `$nodes` and `$targets` parameter. If a plan has parameters called both `$nodes` and `$targets`, they must be set explicitly using named arguments.

    View the [Bolt changelog](https://github.com/puppetlabs/bolt/blob/master/CHANGELOG.md) for more information about Bolt 2.0 features and requirements.


#### Simplified prerequisites for select `puppet infrastructure run` commands

Several `puppet infrastructure run` commands now use the orchestrator, so they typically no longer require configuring SSH as a prerequisite. Affected commands include:

-   Compiler, replica, and agent certificate regeneration

    **Note:** In cases where you're regenerating certificates after recreating your certificate authority, you must still specify SSH connection details, because nodes can't connect to the orchestrator without a valid certificate.

-   Legacy compiler conversion
-   PostgreSQL cleanup after upgrade

#### Simplified certificate regeneration for Windows agents

Certificates for Windows agents can now be regenerated with a single command, `puppet infrastructure run regenerate_agent_certificate`.

#### Services added to status command

The `puppet infrastructure status` command now reports status for PostgreSQL as well as the Bolt and Agentless Catalog Executor \(ACE\) services.

#### Purging a node deletes PuppetDB data for the node

The `puppet node purge` command now removes all data for the node from PuppetDB. Previously, the command deactivated the node but didn't delete it, so the node could still appear in PuppetDB queries.

#### FIPS-compliant PE upgraded to PostgreSQL 11

FIPS-compliant PE installations now use PostgreSQL 11 by default. If you're upgrading an earlier FIPS-compliant PE version, review the [cautions for PostgreSQL upgrade](upgrading_pe.md#).

#### High availability terminology changed to disaster recovery

We've changed the way we refer to failover capabilities within PE, from high availability to disaster recovery. This change more accurately reflects the role of a replica in PE installations, and aligns our terminology with industry standards. Some references to high availability and HA still remain in the software.

#### Platform support

This version adds support for these platforms.

**Agent**

-   Ubuntu 20.04
-   FIPS 140-2 compliant Microsoft Windows Server 2012R2 and 2012R2 core

### Deprecations and removals

#### Text install removed

Text-based installation, where you provide a custom configuration file \(`pe.conf`\), has been removed from the installer. The simplified PE installation process now relies entirely on defaults, apart from specifying a console administrator password. After installation, you can scale or configure your environment as needed.

You can still install PE using a customized `pe.conf` file by running `./puppet-enterprise-installer -c /tmp/pe.conf`.

#### Legacy compilers deprecated

Legacy compilers without the PuppetDB service are deprecated. You can [convert](installing_compile_masters.md#) legacy compilers after upgrading to this version.

### Resolved issues

#### Metrics API could leak sensitive information on Ubuntu platforms

Puppet Server and PuppetDB could leak sensitive information such as hostnames through the metrics API when used on Ubuntu platforms. This version disables the `/metrics/v1` endpoints by default and restricts access to the `/metrics/v2` endpoints to localhost. \([CVE-2020-7943](https://puppet.com/security/cve/CVE-2020-7943/)\)

#### Upgrading Windows agents using the puppet\_agent module could restart non-Puppet services

If you're using a log aggregator, upgrading Windows agents using the puppet\_agent module could cause non-Puppet services to restart. This issue was fixed in Puppet agent version 6.15, which ships in PE 2019.7, however you might still encounter the issue during upgrades from older versions of PE to PE 2019.7 and later. When upgrading in the circumstances described here, time upgrade to minimize the risk of any downtime for Windows nodes.

#### Replica upgrade failed when upgrading between recent PE versions

Replica upgrade failed when upgrading between any PE versions from 2019.2 to 2019.5. The failure occurred because the upgrade script checked for PostgreSQL 9.6 packages, and erroneously attempted to migrate to PostgreSQL 11, even though that migration already occurred. With this release, replica upgrade no longer uses the upgrade script, so the issue doesn't occur.

#### Running tasks or plans could produce a class loading error

When running tasks or plans, you could encounter a class loading error if the orchestration service was reloaded when a restart was required. For example:

cannot link Java class com.puppetlabs.Facter

missing class name \(\`com.puppetlabs.Facter'\)

#### The `wait_until_available` plan action was broken for PCP transport nodes

Because of an issue with the wait functionality for PCP nodes, the `wait_until_available` action was not working for nodes connected to pp-agent through a pcp-broker. Now, `wait_until_available` no longer fails on PCP transport nodes.

#### JSON output for plans didn't parse correctly

When a `run_command` or `run_script` plan action printed JSON to `stdout`, the resulting `stdout` file was null. Plans in PE now correctly return JSON output from actions like `run_command` or `run_script`.

#### Unusable plan function `get resources` had unhelpful errors

The `get resources` function is not yet implemented in PE, but when users ran it, they would get cryptic errors. Now, the function fails with an informative message.

#### Restarting orchestration-services caused pcp-brokers to become unresponsive

When pe-orchestration-services restarts on PE masters, the pcp-brokers connected to master no longer fail when attempting to reconnect to the master after it returns.

#### Undefined behavior for `_run_as` option in plan actions

The `_run_as` option for plan actions has been silently ignored. Now, when you use the option and a plan fails, it raises a helpful error message.

## PE 2019.6

This version wasn't released.

## PE 2019.5

### Resolved issues

#### Metrics API could leak sensitive information

Puppet Server and PuppetDB could leak sensitive information such as hostnames through the metrics API. This version disables the `/metrics/v1` endpoints by default and restricts access to the `/metrics/v2` endpoints to localhost. \([CVE-2020-7943](https://puppet.com/security/cve/CVE-2020-7943/)\)

## PE 2019.4

### Enhancements

#### Docs versions and archives

With this release, we're switching to using **latest** as the version number for the PE update track, rather than using version-specific URLs. Going forward, the **latest** docs will accumulate changes since the last long-term support release. Concurrently, we're archiving docs for end-of-life and superseded versions of PE. We're making these changes to align with the new [PE support lifecycle and versioning cadence](https://puppet.com/docs/puppet-enterprise/product-support-lifecycle/), and hopefully reduce version confusion with older docs on our site.

If you use an EOL or superseded version of the product, don't worry, you can still access docs for older versions. For details, see [Archived PE docs](archived_pe_docs.md#).

#### Console enhancements

In an effort to make the console easier to navigate and understand, we have made the following improvements.

The **Inventory** page has a new look. Instead of using widgets to install agents, nodes, and devices, each installation type has a button that links to its own page. There are also more help icons and definitions on the page.

The plans event view in the **Job details** page now displays an output message for each plan run.

The following pages have new descriptions and more consistent header designs:

-   **Jobs**
-   **Puppet**
-   **Task**
-   **Plan**
-   **Inventory**
-   **Install agent**
-   **Connect nodes over SSH or WinRM**
-   **Connect network devices**
-   **Users**
-   **Roles**
-   **User groups**
-   **License**
-   **Integrations**
-   **Help**

#### Code Manager API module-related keys

Two new keys in the Code Manager `/deploys` endpoint provide greater flexibility in deploying modules.

-   `"deploy-modules"` indicates whether Code Manager deploys modules from the selected environments.
-   `"modules"` enables you to list specific modules to deploy within the selected environments.

#### Middleware endpoint for full out\_message plan content

There is a new endpoint in the orchestrator that retrieves an individual event, with no truncation of the `out_message`. See [GET /plan\_jobs/:job-id/event/:event-id](orchestrator_api_events.endpoint.md#) for more information.

### Resolved issues

#### Default for `strict_hostname_checking` changed to `true`

The default setting for `strict_hostname_checking` in PE was changed to `true` to resolve a security issue. PE users who are not upgrading should manually set `strict_hostname_checking` to `true` to ensure secure behavior. You must also specify the fully qualified domain name of the host when referring to nodes; partial hostname matches, for example `node /^foo/` are no longer supported.

#### Default class parameters had incorrect format in console

Default structured parameters in the console were always interpreted as text, specifically as Puppet code. Now, they are correctly transformed into JSON to allow them to be used directly in a classifier parameter value without modification.

#### **Activity** tab had inconsistent behaviors on **Node details** page

Content in the **Activity** tab on the **Node details** page would appear inconsistently, disappearing and reappearing after clicking on other tabs. The underlying behavior causing this issue has been fixed.

#### Empty bolt.yaml at environment root broke plans

An empty bolt.yaml file in the root of an environment directory will no longer prevent users from viewing and running plans.

#### Restarting pe-orchestration-serives caused pcp-brokers to become unresponsive

When pe-orchestration-services restarts on PE masters, pcp-brokers connected to the master no longer experience failures when attempting to reconnect to the master.

#### Class parameter selection in Firefox had inconsistent behaviors

In the current versions of Firefox, some odd behavior appeared when editing class parameters. The class parameter selection would impact other selections in the class in unexpected ways. As a result, the ability to edit the parameter was removed. You can still edit the parameter value, but cannot choose a different parameter. This resolves the issue in Firefox, simplifies the interaction, and makes the behavior consistent with other pages.

#### PE ports were vulnerable to LOGJAM

PE ports were vulnerable to LOGJAM because they used common, shared Diffie-Hellman primes. The default Diffie-Hellman ephemeral key size has been increased to 2048 for all JVM PE services, like Puppetserver, PuppetDB, etc.

## PE 2019.3

### Enhancements

#### Java 11 upgrade

This version includes an upgrade from Java version 8 to version 11. If you've customized PE Java services, or use plug-ins that include Java code, test PE 2019.3 and later thoroughly in a non-production environment before upgrading.

#### Puppet ensures platform repositories aren't installed in order to prevent accidental agent upgrade

Previously, Bolt users who installed the Puppet 5 or 6 platform repositories could experience unsupported agent upgrades on managed nodes. With this release, Puppet ensures that the release packages for those platforms are not installed on managed nodes by enforcing `ensure => 'absent'` for the packages.

#### Windows install script optionally downloads a tarball of plug-ins

For Windows agents, the agent install script optionally downloads a tarball of plug-ins from the master before the agent runs for the first time. Depending on how many modules you have installed, bulk plug-in sync can speed agent installation significantly.

**Note:** If your master runs in a different environment from your agent nodes, you might see some reduced benefit from bulk plug-in sync. The plug-in tarball is created based on the plug-ins running on the master agent, which might not match the plug-ins required for agents in a different environment.

This feature is controlled by the setting `pe_repo::enable_windows_bulk_pluginsync` which you can configure in Hiera or in the console. The default setting for bulk plug-in sync is `false` \(disabled\).

#### `puppet infrastructure run` commands no longer require an authentication token

`puppet infrastructure run` commands that affect PuppetDB, including `migrate_split_to_mono` and `enable_ha_failover`, no longer require setting up token-based authentication as a prerequisite for running the command. By default, these commands use the master's PuppetDB certificate for authentication.

#### `puppet infrastructure run` commands provide more useful output

`puppet infrastructure run` commands, such as those for regenerating certificates or enabling high availability failover, provide more readable output, making them easier to troubleshoot.

#### Calculations for PostgreSQL settings are fine-tuned

The `shared_buffers` setting uses less RAM by default due to improvements in calculating PostgreSQL settings. Previously, PostgreSQL settings were based on the total RAM allocated to the node it was installed on. Settings are now calculated based on total RAM less the default RAM used by PE services. As a result, on an 8GB installation for example, the default `shared_buffers` setting is reduced from ~2GB to ~1GB.



#### PostgreSQL can optionally be cleaned up after upgrading

After upgrading, you can optionally remove packages and directories associated with older PostgreSQL versions with the command `puppet infrastructure run remove_old_postgresql_versions`. If applicable, the installer prompts you to complete this cleanup.

#### \*nix command for regenerating agent certificates includes a parameter for CRL cleanup

The `puppet infra run regenerate_agent_certificate` command includes a `clean_crl` parameter. Setting `clean_crl` to `true` cleans up the local CRL bundle. When you regenerate certificates for \*nix agents after recreating your certificate authority, you must include this parameter with the value set to `true`. If you're regenerating agent certificates without recreating the CA, you don't need to clean up the CRL.

#### `puppetlabs-pe_bootstrap` task supports Puppet agent on CentOS 8

The `puppetlabs-pe_bootstrap` task that ships in PE has been updated to support Puppet agent installation on CentOS 8.

#### New task targets API

Use the new task targets API to fine-tune task permissions automatically. See [POST /command/task\_target](orchestrator_api_commands_endpoint.md#) and [Puppet orchestrator API: scopes endpoint](orchestrator_api_scopes_endpoint.md#).

#### Console enhancements

These are enhancements to the console in this release:

-   Plan metadata

    View plan metadata and parameters. To view them in the console, type in a name of a plan in the **Plan** field and click **View plan metadata**. To view metadata on the command line, run `puppet plan show <PLAN NAME>`.

-   **Test connections** option

    Test connections for nodes and devices before adding them to your inventory. This option is enabled by default on the **Inventory** page. If a connection fails, you can edit the node or device information and try again.

-   Custom PQL queries

    Add your own custom PQL queries to the console and use them for running Puppet and tasks. See [Add custom PQL queries to the console](running_tasks_in_the_console.md#) for more information.

-   Breadcrumbs

    Pages in the console now have breadcrumbs, showing you where you are in the interface. The breadcrumbs are links you can use to move to parent pages.

-   Transport details

    View the transport mechanism, SSH or WinRM for example, for task runs in the **Connections** and **Activity** tabs on the **Nodes** page.

-   **Run** drop-down menu

    The **Run Puppet on these nodes** button has been replaced with a **Run** drop down menu so you can run Puppetor run a task for the nodes listed on the current page. The new option is available on the **Overview**, **Events**, and **Packages** pages.

-   Ability to select environment for tasks and plans

    When you run a task or a plan in the console, you can now specify an environment other than `production`.

-   Additional run options

    In addition to `no-op`, you can now specify `debug`, `trace`, and `eval-trace` run options when running Puppet.


#### Platform support

This version adds support for these platforms.

**Agent**

-   Fedora 31

### Deprecations and removals

#### Deprecated platform support

Support for these platforms is deprecated in this release and will be removed in a future version of PE:

**Master**

-   Enterprise Linux 6
-   Ubuntu 16.04

#### Razor deprecated

Razor, the provisioning application that deploys bare-metal systems, is deprecated in this release, and will be removed in a future version. If you want to continue using Razor, you can use the [open source version](https://github.com/puppetlabs/razor-server) of the tool.

#### Node graph removed

The node graph in the console has been removed due to infrequent use. The graph was used to view relationships between resources and classes within a node catalog. To generate a node graph now, use the Puppet [VS Code extension](https://puppet-vscode.github.io/docs/features/puppet-commands/).

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

#### Code manager could not deploy Forge modules with a proxy

The commands `puppet code deploy` and `r10k` failed when behind a proxy. The commands didn't use the configured proxy settings and using them would result in problems downloading modules from the Puppet Forge. This was due to an issue in a dependency gem.

Now, the commands work behind a proxy.

#### Orchestrator error message included Bolt command suggestions

When a plan or task was not found, the resulting error message gave a suggestion to run `bolt {plan,task} show`, which is unhelpful in PE. The error message no longer shows the Bolt command suggestion.

#### `bolt.yaml` plans did not work in PE

Plans with `bolt.yaml` in the root directory of the environment will no longer fail. Don't use the `modulepath` setting in `bolt.yaml`, because it may lead to unintended consequences when loading tasks and plans.

#### Ed25519 SSH keys couldn't be used to run task on agentless node

Running a task on an agentless node using an ed25519 SSH keys would result in an error.

