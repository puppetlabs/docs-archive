---
author: Kari Halsted <kari@puppet.com\>
---

# What's new since PE 2016.4

If your organization adopts long-term supported \(LTS\) Puppet Enterprise releases, the release of PE 2018.1 means that you'll soon be upgrading from our previous LTS release, PE 2016.4. This page summarizes the major new features, notable enhancements, deprecation and removals, and high-profile bugs fixes since PE 2016.4 that make 2018.1 a big step forward in your automated configuration management experience.

This list provides you with overview of the features and concepts introduced in PE since 2016.4, so you have information you'll need to plan your organization's upgrade to 2018.1. It's not intended to be exhaustive, or to be specific about which short-term supported release each item was added in. For detailed release-by-release breakdowns of all fixes and known issues with Short Term Supported PE releases, see their individual release notes:

|Individual release notes|[2016.5](https://www.puppet.com/docs/pe/2016.5/release_notes.html)|[2017.1](https://www.puppet.com/docs/pe/2017.1/release_notes.html)|[2017.2](https://www.puppet.com/docs/pe/2017.2/release_notes.html)|[2017.3](https://www.puppet.com/docs/pe/2017.3/release_notes/new_features.html)|[2018.1](pe_new_features.md#)|

Get familiar with the latest hardware requirements, supported operating systems and browsers, and network configuration details in [System requirements](system_requirements.md).

For information about long and short term support, support levels, and end-of-life dates, see [Puppet Enterprise support lifecycle](https://puppet.com/misc/puppet-enterprise-lifecycle).

## New features since 2016.4

-   **High availability for monolithic installations**

    Ensures that your system remains operational even if certain infrastructure components become unreachable. With high availability enabled, Puppet runs fail over to a replica if your master or master of masters is unreachable. If your master is permanently disabled, you can promote the replica to serve as the new master. See [Configuring high availability](configuring_high_availability.md).

-   **Backup and restore**

    Simple commands that back up your  PE infrastructure, including  Puppet code, configuration,  PuppetDB, and certificates, allowing you to more easily migrate to a new master or recover from system failures. For monolithic installations only, not split. See [Backing up and restoring Puppet Enterprise](backing_up_and_restoring_pe.md).

-   **Run Puppet from anywhere**

    Running Puppet isn't just for the command line anymore. Many pages in the console have **Run Puppet** buttons that let you launch runs on demand. See [Running Puppet on demand](running_puppet_on_demand_with_orch.md).

-   **Hiera 5**

    Environment- and module-level data, allowing any environment or module to include its own `hiera.yaml` file and its own data sources. A new backend system that simplifies integrating custom data sources. Improved performance and better debugging, with complete explanations where Hiera looked and where it found values. A new optional HOCON data backend.

    Hiera 5 was brought into the 2016.4 stream in a fix release, so you might be using some of its features already. If you're still using the deprecated Hiera 4 features, it's definitely time to move to Hiera 5 instead.  Hiera 3 features are still supported. For details, see [Upgrading to Hiera 5](https://puppet.com/docs/puppet/latest/hiera_migrate.html).

-   **Orchestrator in the console**

    With orchestrator integrated into the console, you can set up jobs with ease, and use the console’s reporting and infrastructure monitoring tools to review jobs and dig deeper into node run results.

-   **Orchestrator API endpoints for tracking multiple jobs**

    Two commands endpoints have been added to orchestrator API: `plan_start` and `plan_task`. You use these endpoints to track jobs run together as part of a plan. \(A plan combines multiple tasks and runs them with a single command. For more information, see the docs for [Bolt](https://puppet.com/docs/bolt/0.x/bolt.html).\)

-   **Packages inventory and management**

    View a filterable inventory of all packages installed on your nodes, and learn which nodes are using each package version. See which packages are not managed by Puppet and run tasks to update them. Quickly navigate to Puppet code for packages that are managed. See [Viewing and managing all packages in use](viewing_packages_in_use.md#).

-   **Internationalization support**

    Improved UTF-8 support, plus a Japanese console, services, and resources. See [Translated PE](translated_pe.md#).

-   **Ad hoc \(arbitrary\) tasks**

    You can run arbitrary, one-off tasks from the console, on the command line, or by the orchestrator API, targeting an individual node, a list of nodes, or a set of nodes derived from a  PQL query. Watch task execution in real time from the console or command line, and take advantage of built-in job reporting and activity service that shows a complete history of task jobs. Role-based access control allows you to define who can run which tasks on which nodes. See [Running tasks](running_tasks.md).

-   **Hiera overrides in the console**

    You can set parameters on node groups without declaring the class. Data that is set in the console is used for automatic parameter lookup, which promotes code regularity and predictability. See [Define data used by node groups](grouping_and_classifying_nodes.md#).

-   **A new home for the docs**

    PE docs moved to [puppet.com/docs](https://puppet.com/docs) and got somewhat restructured and reorganized. Update your bookmarks! Most old links should automatically redirect to the new locations, but let us know if you can't find something.


## Installation and upgrade enhancements

-   On Enterprise Linux systems, if you have a proxy between the agent and the master, you can now use the install script to specify an `http_proxy_host` to be used during package installation, for example `-s agent:http_proxy_host=<PROXY_FQDN>`. Previously, specifying a proxy host using the install script added the setting to `puppet.conf` without using it for installation.

-   You can now control the state of the Puppet service when you install \*nix or Windows agents with an install script. This capability enables manually kicking off the initial Puppet run or doing so with a provisioning system. Use these flags to control the Puppet service:

    |Option|\*nix|Windows|Values|
    |------|-----|-------|------|
    |ensure|`--puppet-service-ensure <VALUE>`|`-PuppetServiceEnsure <VALUE>`|    -   runnng

    -   stopped

|
    |enable|`--puppet-service-enable <VALUE>`|`-PuppetServiceEnable <VALUE>`|    -   true

    -   false

    -   manual

    -   mask

|

-   The simplified agent install script for Windows now supports setting certain MSI properties as flags in the PowerShell script. You can combine agent configurations with MSI properties.

    |MSI Property|PowerShell flag|
    |------------|---------------|
    |`INSTALLDIR`|`-InstallDir`|
    |`PUPPET_AGENT_ACCOUNT_USER`|`-PuppetAgentAccountUser`|
    |`PUPPET_AGENT_ACCOUNT_PASSWORD`|`-PuppetAgentAccountPassword`|
    |`PUPPET_AGENT_ACCOUNT_DOMAIN`|`-PuppetAgentAccountDomain`|

-   PE 2017.3 upgraded PostgreSQL to version 9.6. If you use an external PostgreSQL instance, you must upgrade it before you upgrade PE. If you're upgrading with high availability enabled, you must upgrade and then forget the existing replica, and provision and enable a new replica.

    Ensure you have the right amount of free disk space before you upgrade. Plan for a downtime window of a couple hours if you have a large database, and don't worry if your upgrade process seems to hang while upgrading the database—it's not hung. After you upgrade, after everything checks out as working, clean up the old version 9.4 database to free up disk space.

-   To support Ruby 2.3, PE 2018.1 changes the default setting for JRuby 9k to enabled \(`puppet_enterprise::master::puppetserver::jruby_9k_enabled: true`\). This default differs from open source Puppet and from previous versions of PE.

    **Important:** When upgrading to this version of PE, you must update any server-side installed gems or custom extensions to be compatible with Ruby 2.3 and JRuby 9k. For example, if you're using the autosign gem workflow, upgrade the gem to 0.1.3 and make sure you're not using yardoc 0.8.x. For more information about autosigning, see [Autosigning certificate requests](https://puppet.com/docs/puppet/5.5/ssl_autosign.html). See [SERVER-2161](https://tickets.puppetlabs.com/browse/SERVER-2161) for details on the switch from JRuby 1.7 to JRuby 9k.

    If you notice issues with JRuby in PE, [file a ticket](https://tickets.puppetlabs.com/secure/Dashboard.jspa) rather than changing the default parameter setting to avoid issues when this setting is eventually deprecated.

-   You can now securely install Windows agents by [installing using a certificate](installing_agents.md#).

-   On Red Hat, Ubuntu, SUSE Linux Enterprise Server, Solaris, and AIX platforms, if you manually transfer CA certificates to agents and [install using the --cacert flag](installing_agents.md#) to point to the master CA, subsequent downloads invoked by the installation script are now secured.

-   Previously, compile masters downloaded agent packages from puppet.com to make them available for agent installs, meaning they had to reach the internet to retrieve those packages. Compile masters now retrieve agent packages directly from the master of masters.

-   Installer timestamps now include the offset from coordinated universal time \(UTC\) per ISO 8601 instead of the Java %date format previously used.

-   Use Puppet Development Kit \(PDK\) to test your modules for compatibility with  PE before upgrading, and update or convert modules as needed.


## Configuration enhancements

-   A new **PE Infrastructure** node group, **PE Database**, enables setting class parameters to control database configuration. Using the **PE Database** node group to specify parameters adds the new value to `pe.conf` and ensures that your settings persist through upgrades.

-   You can customize the target URL for HTTP redirects using the setting `puppet_enterprise::profile::console::proxy::http_redirect::server_name`.

-   Most of PE’s services \(including pe-puppetserver, pe-puppetdb, pe-console-services, and pe-orchestration-services\) now have a `reload` action, which acts like a restart but is significantly faster. If you need to refresh a service after changing its configuration, you can almost always reload it instead of restarting.

    To reload a service, run `service <NAME> reload` instead of `service <NAME> restart`. The `reload` action restarts a service without restarting its underlying Java Virtual Machine \(JVM\) process. Since starting the JVM is the most time-consuming part of a restart, the speed improvement is very noticeable.

    Some configuration changes require a full restart. These are:

    -   [Changes to Java arguments](config_java_args.md#), like heap size.

    -   Changes to Puppet Server’s `[ca.cfg](https://puppet.com/docs/puppetserver/5.3/configuration.html) file`, or anything else in a `services.d` directory.

-   A new `puppet infrastructure status` command displays errors and alerts from PE services, including the activity, classifier, and RBAC services, Puppet Server, and PuppetDB.

-   The new `puppet_enterprise::ssl_cipher_suites` parameter sets the SSL cipher suites for core Puppet services. This parameter expects an array of SSL ciphers, for example:

    ```
    puppet_enterprise::ssl_cipher_suites: ['ECDHE-ECDSA-AES256-GCM-SHA384', 'ECDHE-RSA-AES256-GCM-SHA384', 'ECDHE-ECDSA-CHACHA20-POLY1305']
    ```

    Console SSL ciphers are managed separately through the new `puppet_enterprise::profile::console::proxy::ssl_ciphers` parameter.

    Cipher names are in RFC format.

-   The new `puppet_enterprise::profile::database::auto_explain_settings` parameter lets you enable and configure auto explain for PE-PostgreSQL using a hash of auto-explain settings. For example:

    ```
    puppet_enterprise::profile::database::auto_explain_settings: 
     auto_explain.log_min_duration: '10s'   
     auto_explain.log_verbose: true
    ```

    For details about auto explain settings, see the[PostgreSQL documentation](https://www.postgresql.org/docs/9.6/static/auto-explain.html).

-   The new `puppet_enterprise::license::manage_license_key` parameter, when set to `false`, lets you manage your PE license key with your own custom Puppet code, rather than manually copying the license key to `/etc/puppetlabs/license.key`.
-   This version of PE changes the translation default to enabled \(`puppet_enterprise::master::disable_i18n: false`\), providing translated logs, reports, and some command-line interface text in Japanese. To see translated strings, your system locale and browser language must be set to Japanese, and for the text-based installer, you need [gettext](https://www.gnu.org/software/gettext/).


## Orchestrator enhancements

-   You can run Puppet or tasks from a node group in the console using the **Run** control. If there are no nodes in a group, the control is deactivated. If you only have permission to run Puppet, or to run tasks, then the control changes to a button for the specific type of job you can run.

-   When using the `puppet task` or `puppet job` commands on the orchestrator CLI, you can pass PQL queries or node list targets in a text file by specifying the full path to the file prefixed with @ \(for example, `puppet job run --nodes @/path/to/file.txt`\). You can also use a text file to pass parameters, in JSON format, when using the `puppet task` command.

-   The `puppet job run` command now accepts a `--description` flag. The job description is displayed when you run `puppet job show <JOB_ID>`.

-   In the console, you can now also create jobs from the **Overview**, **Events**, and **Classification** node groups pages, instead of only the **Jobs** page.

-   You can select any job from the job details page and rerun it without having to recreate the settings from the original job. Jobs with application targets run from the command line cannot be rerun from the console, as the console doesn’t support application targets.

-   The orchestrator includes a run mode in which you can override an agent's `noop = true` setting \(set in `puppet.conf`\). When you use this run mode, all nodes run in enforcement mode, and a new catalog is enforced on all nodes. In the console, run mode is available as a job setting. On the command line, use the `--no-noop` flag.

-   The Puppet orchestrator communicates with PCP brokers on compile masters on port 8143 and sends job-related messages to the brokers, which are then relayed by the brokers to PXP agents. As you add compile masters, you’re able to scale the number of PCP brokers that can send orchestration messages to agents. See [Configure compile masters for orchestration scale](installing_compile_masters.md#) for instructions.

-   Use the PXP agent log file to debug issues with the Puppet orchestrator. You can [change its location](config_orchestration.md#) from the default as needed.

-   The `pe-puppetserver` service now defaults to an open file limit of `12000` to support orchestrator scale with PCP brokers.

-   A new flag, `--no-enforce-environment`, ensures the orchestrator will ignore the environment set by the `--environment` flag for agent runs. When you use this flag with the `puppet job run` command, agents run in the environment specified by the PE Node Manager or their `puppet.conf` file.


## PuppetDB enhancements

-   Using the `facts_blacklist` setting in Hiera, you can now specify a list of facts that should not be stored in the PuppetDB database. See [Configure blacklisted facts](config_puppetdb.md#).

-   Use the `puppetdb_query` function to query PuppetDB data from Puppet code. See [the PQL documentation](https://puppet.com/docs/puppetdb/5.2/api/query/tutorial-pql.html#querying-with-puppet-code) for more information.

-   By default, PuppetDB excludes deactivated or expired nodes from query results. You can change this behavior [by setting the `node_state` query parameter](https://puppet.com/docs/puppetdb/5.2/api/query/v4/index.html#pdbqueryv4).

-   PuppetDB now uses 14 days for a default time-to-live value \(`node-purge-ttl`\) before it deletes nodes that have been deactivated or expired. You can [change this default behavior](config_puppetdb.md#).

-   PuppetDB now stores incoming commands in [stockpile](https://github.com/puppetlabs/stockpile) rather than  ActiveMQ, increasing performance and reliability. 


## Console and console services enhancements

-   On a package's detail page, you can sort package instances by operating system version and environment.

-   Task metadata is available in the console. On the **Run a task** page, when you select a task to run, you can click **view task metadata** to open an info pane of task and parameter information.

-   Enhanced behavior of sensitive parameters in tasks. If you mark a parameter as sensitive, its value will not be displayed in logs or API responses when the task runs. In addition, parameters marked as sensitive now appear with the value hidden, so you can rerun the job with the parameter.

-   Added conflict detection for node groups. When you commit changes to a node group, you are alerted if another user has made changes to the group while you were editing it. To support this enhancement, two keys have been added to the node classifier service API: `serial-number` and `last-edited`.

-   A sortable **Job ID** column appears in the run status table on the **Overview** page. The ID number of the job a node was most recently part of is displayed in this column.

-   On the **Permissions** tab in the **User Roles** page, the user permissions object type has been changed from **Orchestrator** to **Job orchestrator** and its permissions from **View orchestrator** to **Start, stop and view jobs**.

-   Puppet run metrics for each node are collated and available under a **Metrics** tab. On the **Reports** page, click the **Report time** for a node, and then select the **Metrics** tab.

-   In the console's node detail screen, keys of structured facts are now sorted alphabetically, making them more legible and scannable.

-   A new **Packages** tab on each node's inventory page shows the complete list of installed packages with sortable version, provider, and Puppet management information.

-   We've improved console performance by implementing report count caching on the **Reports** and **Overview** pages.

-   The console now redirects to HTTPS when you attempt to connect over HTTP. The `pe-nginx` web server now listens on port 80 by default. You can [disable the HTTPS redirect](config_console.md#) in Hiera.

-   Previously, the node classifier service stored a check-in for each node when its classification was requested. The check-in included an explanation of how the node matched the rule of every group it was classified into. This functionality created performance issues when managing a large deployment of nodes. The check-in storage is still available, but it’s now disabled \(`false`\) by default. You can enable this by setting `puppet_enterprise::profile::console::classifier_node_check_in_storage` to `true` in the [console](config_console.md#).

-   In this release, you can determine the amount of time that should pass after a node sends its last report before it is considered unresponsive. Set an integer to specify the value in seconds. The default is 3600 seconds \(one hour\). Adjust `puppet_enterprise::console_services::no_longer_reporting_cutoff` in the [console](config_console.md#).

-   The `ping_interval` setting controls how long PXP agents will ping PCP brokers. If the agents don’t receive responses, they will attempt to reconnect. The default is 120 seconds \(two minutes\). Adjust `puppet_enterprise::pxp_agent::ping_interval` in the [console](config_console.md#).

-   We’ve redesigned the console’s navigation pane, and reduced its width by half.

-   Quickly access the run report associated with a particular event by using the **View run report** link that now appears on the Events detail page.

-   The fact value filters on the **Overview** and **Reports** pages now display warning messages if you attempt to use an invalid regular expression, invalid string operator, or empty fact name.

-   You are no longer logged out of your console session when you restart the `pe-console-services` service.

-   We have enhanced the usability of the node graph, including updates to controls on the details pane and dependency view.

-   Corrective change reporting has been added to the node graph and the Events page in the console.

-   The node graph and **Events** page now provide information regarding whether a Puppet run was completed in enforcement or no-op mode, and whether changes were enforced or simulated.


## Code management enhancements

-   The new `ignore_branch_prefixes` subsetting lets you designate specific environments that should not be deployed.

-   Two new flags that specify whether local changes to Git modules should be overwritten have been added to r10k. Use `r10k puppetfile install --force` to overwrite local changes when installing updates, and `r10k deploy --no-force` to preserve local changes on deploy.

-   A new file sync reset procedure allows you to more easily recover from a failure state.

-   We’ve added a new Code Manager parameter, `deploy-ttl`. This parameter specifies the length of time completed deployments are retained before garbage collection, which is important to ensuring consistent Code Manager performance over time.

-   We’ve added a new flag, [`--dry-run`](puppet_code.md#), to the `puppet-code` command. When you run `puppet-code` with this flag, it tests connections to your control repos and returns a consolidated list of all environments in the control repos.

-   The behavior of the `--wait` flag used with the `puppet-code` command has been updated to improve accuracy and completeness of reporting. Previously, `--wait` returned results after deploying code to the code-staging directory. The flag now waits for file sync to also deploy code to the live code directory on all compile masters before returning results.

    Due to this updated behavior, running `puppet-code deploy` with `--wait` takes a minimum of 10 seconds longer than in previous versions. In deployments that are geographically dispersed or have a large quantity of environments, completing code deployment can take up to several minutes.

-   A [`status` action](puppet_code.md#) in the `puppet-code` command verifies from the command line that Code Manager and file sync are responding.

-   Code Manager provides environment isolation for your resource types. Generated metadata files ensure that each environment uses the correct version of the resource type. For more information, see [How Code Manager works](code_mgr_how_it_works.md#).


## RBAC and activity service enhancements

-   A user’s activity log now records when they run Puppet from the orchestrator CLI or from the **Run Puppet** button in the console.

-   The `puppet access login` command can now log in using the console `admin` account.

-   The **Run Puppet on agent nodes** permission includes the ability to trigger a Puppet run from the console or orchestrator. See [RBAC available permissions](rbac_permissions_intro.md#).

-   The activity service event reporting includes agent runs that are part of [orchestration jobs](activity_api_event_types.md#).


## Security enhancements

-   You can revoke and reinstate access to PE for the Administrator account.

-   Administrators can assign permissions to a user role to run tasks on all nodes or a selected node group.

-   Removed access controls from the console for users without permission to use them. Previously unauthorized users could view these controls but not use them.

-   Hostname and wildcard configuration options added to the RBAC directory services to validate that the certificate and the hostname for the connecting client match.

-   Connections to PE databases can now be made only with certificates. User names and passwords are no longer used by default.

-   Unlabeled RBAC tokens stored in the database are now hashed. If you label tokens, they are stored unencrypted.

-   For those with security compliance needs, PE now supports [disabling TLSv1](disable_tlsv1_in_PE.md). Services in PE support TLS versions 1, 1.1, and 1.2.

-   The MCollective package agent plug-in helps you install packages from any source \(including a URL\) and does not require that the packages are signed. This provides a `peadmin` user the ability to execute arbitrary code on any MCollective server.

-   A default action policy has been put into place in PE that disallows using the package `install`, `uninstall`, and `purge` actions. The policy can be modified and additional action policies can be added using the `puppet_enterprise::profile::mcollective::agent::allowed_actions` parameter to specify agent plug-ins you want to apply an action policy to, and a list of the actions you want to explicitly allow.

-   MCollective client keys are labeled sensitive and will not be stored in PuppetDB.

-   Use the [`certregen` module](https://forge.puppet.com/puppetlabs/certregen), available on the Forge, to regenerate and redistribute Puppet CA certificates that are expiring soon, as the Puppet CA cert expires after five years. Refer to the module’s README for full instructions.


## Razor enhancements

-   The latest Razor client, version 1.8.1, removes the incompatibility with PE and standardizes the client \(`razor-client`\) for use with either PE or the open source Razor server.

-   The Razor client is now supported on Windows 2016 Servers.

-   Razor now includes supported tasks for SUSE Linux Enterprise Server 11 and 12, Fedora 23, Windows 2016, Ubuntu 16.04, and VMware ESXi 6.

-   The shiro.ini file used to enable authentication security now uses SHA-256 credential matching by default to specify password hashes.
-   A new `has_macaddress_like` tag operator can be used as a regular expression to match hardware MAC addresses.

-   New task template helpers `repo_file_contents (PATH)` and `repo_file? (PATH)` can be used to read and check the existence of repo files hosted on mirrors and created with the `--url` argument.
-   The `node` task template helper can now be used to evaluate whether a node booted via UEFI, for example `node.hw_hash['fact_boot_type'] == "efi"`.
-   Razor now supports Windows installation using international ISOs. When you create a Windows policy to provision non-English systems, use the `node_metadata` attribute to specify a locale.

-   [New commands](using_the_razor_client.md#) let you update an existing policy’s repository, broker, or node metadata. For example, without re-creating any policies, you can add a broker to a policy that used the `noop` broker, or switch to the `puppet-pe` broker when you upgrade to Puppet Enterprise.

-   In the [`create-broker`](using_the_razor_client.md#) command, a new `ntpdate_server` property in the `configuration` attribute lets you specify an NTP server. The server is used to synchronize the date and time before installing the agent, which prevents certificate errors.

-   In the [`modify-node-metadata`](using_the_razor_client.md#) command, a new `force` attribute lets you bypass errors in a batch operation with `no_replace`. Existing keys aren’t modified.


## Puppet Server enhancements

-   The `max_requests_per_instance` setting, which controls the maximum number of requests per instance of a JRuby interpreter, increased from 10,000 to 100,000. This change provides a performance boost while still clearing short-lived environments.

-   Puppet Server no longer requires a restart or reload in order to enforce certificate revocation.

-   Puppet Server now uses Jetty 9.4.

-   Puppet Server now uses JRuby 9.1.16.0 or later by default.

-   The sample Grafana dashboard for Puppet Server metrics visualizes new metrics.

-   Puppet Server now has a `max_queued_requests` setting that can be used to control a [thundering herd problem](https://en.wikipedia.org/wiki/Thundering_herd_problem).


## Analytics enhancements

-   Puppet Enterprise collects data about your PE installation and sends it to Puppet so we can improve our product. In addition to previously collected analytics, we now also collect basic information about:

    -   agents

    -   console usage

    -   node groups

    -   orchestrator jobs

    -   Amazon Web Services Marketplace Image use

    -   Puppet Server

    -   Cloud platform and hypervisor use

    -   All-in-one `puppet-agent` package version

    -   Use of MCollective and non-default user roles

    -   JVM memory usage

    -   Certificate autosign setting

    For details about what data we collect and how to opt out, see [Analytics data collection](analytics_overview.md#).


## Other enhancements

-   In order to keep environment-specific data within the environment folder, the default global `hiera.yaml` now supports console data only and doesn't include the previous YAML file hierarchy. If you rely on the default global YAML file hierarchy of `nodes/%{clientcert}.yaml` and `common.yaml`, create a Hiera 5 compatible `hiera.yaml` file in your control repo environment folder instead. For example: [https://github.com/puppetlabs/control-repo/blob/production/hiera.yaml](https://github.com/puppetlabs/control-repo/blob/production/hiera.yaml)

-   You can now purge nodes without running Puppet on your master and reloading it. However, if you use compile masters, you must still run Puppet on all compile masters in order to revoke a node's certificate and have the change take effect.

-   On non- Windows systems, MCollective server logs now appear in `/var/log/puppetlabs/mcollective`, consistent with other log files.

-   Java garbage collection logs can help you diagnose performance issues with JVM-based PE services. Garbage collection logs are now enabled by default in PE, and the results are captured in the support script, but you can [disable them](config_java_args.md#) if you need to.

-   To help with troubleshooting, you can customize the MCollective client logging level either in the console or in `pe.conf` by setting `puppet_enterprise::profile::mcollective::peadmin::mco_loglevel` to debug, warning, or error instead of the default info.


## Notable bug fixes

-   Puppet runs that are halted by provider errors no longer show up as successful in the console.

-   RBAC no longer defaults to searching nested LDAP groups. This improves login times when connected to large directory trees.

-   The `puppet infrastructure configure` command no longer hangs if the production environment is missing.

-   Numerous issues related to installing PE on nodes behind proxy servers have been fixed.

-   We improved PuppetDB performance for structured facts that change often.


## Notable deprecations and removals

-   MCollective is deprecated. See the important note in the [MCollective documentation](managing_mcollective.md) for details about support and what actions to take if you use MCollective. MCollective is no longer installed by default for new installations. If you use MCollective and you take a migration approach to upgrading—you install the new version from scratch and move new agents over to it—you must enable MCollective either as you install PE 2018.1 or before you migrate the agents.

-   Several platforms are no longer supported or are nearing the end of their support by PE. See [System requirements](system_requirements.md) for the latest supported platforms lists. 

-   Several parameters involving managing external databases are deprecated:
    -   `puppet_enterprise::activity_database_user`
    -   `puppet_enterprise::classifier_database_user`
    -   `puppet_enterprise::orchestrator_database_user`
    -   `puppet_enterprise::rbac_database_user`
-   The following orchestration flags, which were deprecated in previous releases, are removed:

    -   `puppet-job list` is now `puppet-job show`
    -   `puppet-job --env` is now `puppet-job --environment`
    -   `puppet-job App[inst]` is now `puppet-job -a App[inst]`
-   `~/.puppetlabs/etc/puppet/orchestrator.conf` is no longer a valid orchestration config file location. Instead use `~/.puppetlabs/client-tools/orchestrator.conf`

-   The `puppet enterprise configure` command was renamed to `puppet infrastructure configure`.

-   The `whole_environment` orchestration target is removed. Jobs with that target cannot be run from the console or command line.


-   The unsupported option to disable file sync while keeping Code Manager enabled is removed.

-   The `file_sync_repo_id` and `file_sync_auto_commit` Code Manager parameters are removed. PE ignores these parameters and raises a warning if you have set them.


