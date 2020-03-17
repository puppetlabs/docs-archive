---
author: Melissa Amos <melissa.amos@puppet.com\>
---

# PE release notes

These are the new features, enhancements, resolved issues, and deprecations in this version of PE.

**Parent topic:**[Release notes](release_notes_pe_index.md)

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

PE ports were vulnerable to LOGJAM because they used common, shared Diffident-Hellman primes. The default Diffident-Hellman ephemeral key size has been increased to 2048 for all JVM PE services, like Puppetserver, PuppetDB, etc.

#### Disable metrics v1 endpoint and restrict v2 to localhost

To prevent information exposure as a result of [CVE-2020-7943](https://puppet.com/security/cve/CVE-2020-7943), the `/metrics/v1` endpoints are disabled by default, and access to the `/metrics/v2` endpoints are restricted to localhost.

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

`puppet infrastructure run` commands that affect PuppetDB, including `migrate_split_to_mono`, `convert_legacy_compiler`, and `enable_ha_failover`, no longer require setting up token-based authentication as a prerequisite for running the command. By default, these commands use the master's PuppetDB certificate for authentication.

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

