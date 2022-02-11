# Enhancements

These are the enhancements added to PE 2018.1.x.

## Puppet ensures platform repositories aren't installed in order to prevent accidental agent upgrade \(2018.1.12\)

Previously, Bolt users who installed the Puppet 5 or 6 platform repositories could experience unsupported agent upgrades on managed nodes. With this release, Puppet ensures that the release packages for those platforms are not installed on managed nodes by enforcing `ensure => 'absent'` for the packages.

## Windows install script optionally downloads a tarball of plug-ins \(2018.1.12\)

For Windows agents, the agent install script optionally downloads a tarball of plug-ins from the master before the agent runs for the first time. Depending on how many modules you have installed, bulk plug-in sync can speed agent installation significantly.

**Note:** If your master runs in a different environment from your agent nodes, you might see some reduced benefit from bulk plug-in sync. The plug-in tarball is created based on the plug-ins running on the master agent, which might not match the plug-ins required for agents in a different environment.

This feature is controlled by the setting `pe_repo::enable_windows_bulk_pluginsync` which you can configure in Hiera or in the console. The default setting for bulk plug-in sync is `false` \(disabled\).

## `puppet infrastructure run` commands no longer require an authentication token \(2018.1.12\)

`puppet infrastructure run` commands that affect PuppetDB, including `migrate_split_to_mono` and `enable_ha_failover`, no longer require setting up token-based authentication as a prerequisite for running the command. By default, these commands use the master's PuppetDB certificate for authentication.

## `puppet infrastructure run` commands provide more useful output \(2018.1.12\)

`puppet infrastructure run` commands, such as those for regenerating certificates or enabling high availability failover, provide more readable output, making them easier to troubleshoot.

## Calculations for PostgreSQL settings are fine-tuned \(2018.1.12\)

The `shared_buffers` setting uses less RAM by default due to improvements in calculating PostgreSQL settings. Previously, PostgreSQL settings were based on the total RAM allocated to the node it was installed on. Settings are now calculated based on total RAM less the default RAM used by PE services. As a result, on an 8GB installation for example, the default `shared_buffers` setting is reduced from ~2GB to ~1GB.



## Platform support

This version adds support for these platforms.

**Agent**

-   Fedora 31

## The `puppet infrastructure run` command no longer requires the `caserver` parameter \(2018.1.11\)

The `caserver` parameter is no longer required for `puppet infrastructure run` commands that are run from your master.

## Specify alternate DNS names when regenerating certificates \(2018.1.9\)

When regenerating agent or master certificates, you can now pass an optional `dns_alt_names` parameter to the Bolt task.

## `puppet backup create` skips unnecessary database validation \(2018.1.8\)

The `puppet backup create` command now validates your PostgreSQL database only if you specify a `scope` of `puppetdb` or `config`. This change prevents errors if, for example, you specify only `scope=certs`, as you might in a split installation.

## Platform support \(2018.1.8\)

This version adds support for these platforms.

**Agent**

-   Enterprise Linux 8

-   macOS 10.14 Mojave


**Network device agent**

-   Enterprise Linux 8


## Improved RBAC API log messages \(2018.1.8\)

The RBAC service log entries for revoked users attempting to log in now includes the username and UUID.

## Configuration settings preserved throughout upgrade \(2018.1.7\)

Configuration data that you've set in the console and Hiera is backed up every 30 minutes at `/etc/puppetlabs/enterprise/conf.d/user_data.conf` and restored when you upgrade.

## PE uninstaller no longer uninstalls related products, like Bolt \(2018.1.7\)

The PE uninstaller now purges only `/opt/puppetlabs/puppet` and `/opt/puppetlabs/server`, leaving behind `/opt/puppetlabs` if there are any other files remaining in this directory. Leaving this directory in place, as necessary, prevents inadvertently removing other Puppet products, such as Bolt.

## Preconfigured environment node groups \(2018.1.7\)

For new installations, we now package a more user-friendly set of preconfigured environment node groups, including:

-   **All environments**
    -   **Development environment**

        -   **Development one-time run exception**

    -   **Production environment**

If you're an existing PE user, your environment node groups are not affected by upgrade.

## Simplified upgrade with high availability enabled \(2018.1.7\)

For upgrades from 2017.3 and later with high availability enabled, you now just run the upgrade script on your replica and reinitialize the replica. Upgrades from versions earlier than 2017.3 still require forgetting and recreating the replica in order to update to the latest PostgreSQL version.

## Simplified process for forgetting high availability replicas \(2018.1.7\)

The command to forget a high availablity replica, `puppet infrastructure forget`, now includes steps to run Puppet on the master and purge the node as an agent.

## Web-based installation no longer affects `known_hosts` file \(2018.1.7\)

The web-based installer now saves unrecognized SSH signatures in its own file, instead of in the user SSH `known_hosts` file. Previously, when you used the web-based installation method to install on a remote node that wasn't in the `known_hosts` file, the node's signature was added to that file.

## Console addition of `facts-blacklist-type` parameter \(2018.1.7\)

The `facts-blacklist-type` parameter is now a configurable parameter in the console. You can set this parameter to either `literal` or `regex`, as appropriate for the values in `facts-blacklist`. 

## Console rebranding \(2018.1.7\)

The color palette for Puppet Enterprise has been updated to align with company branding and adhere to W3C Web Content Accessibility Guidelines \(WCAG\) 2.0.

## Platform support \(2018.1.7\)

This release adds support for these platforms.

**Agent**

-   Fedora 29

    **Note:** MCollective isn't supported on Fedora 29 agents.


## Platform support \(2018.1.5\)

This release adds support for these platforms.

**Agent**

-   SUSE Linux Enterprise Server 15

-   Windows Server 2019


## Uninstall script on \*nix nodes \(2018.1.5\)

By default, the uninstall script is now copied to all \*nix nodes, including infrastruture nodes. You no longer have to manually copy the script from your master when you want to uninstall a \*nix node.

## \*nix bulk plugin sync with the install script \(2018.1.5\)

For \*nix agents, the agent install script can now download a tarball of plugins from the master before the agent runs for the first time. Depending on how many modules you have installed, bulk plugin sync can speed agent installation significantly.

**Note:** If your master runs in a different environment from your agent nodes, you might see some reduced benefit from bulk plugin sync. The plugin tarball is created based on the plugins running on the master agent, which might not match the plugins required for agents in a different environment.

This feature is controlled by the setting `pe_repo::enable_bulk_pluginsync` which you can configure in Hiera or in the console. Bulk plugin-sync is set to `false` \(disabled\) by default.

## `pe.conf` synched to high availability replicas \(2018.1.5\)

The `enterprise/conf.d` directory, including the `pe.conf` file, is now automatically synched from masters to replicas in high availability installations. This expands the available methods for specifying configuration parameters in HA configurations. Previously, we recommended using only Hiera to specify configuration parameters. With this addition, you can now use Hiera or `pe.conf`.

## Platform support \(2018.1.3\)

This release adds support for these platforms.

**Master**

-   Ubuntu 18.04


**Agent**

-   Ubuntu 18.04

-   Fedora 28

    **Note:** MCollective isn't supported on Fedora 28 agents.


## Agent repositories \(2018.1.3\)

Agent tarballs for this PE version now contain repositories that better match how open source agents are built and shipped. The puppet\_agent module version 1.6.2 includes repository updates. 

**Important:** If you manage Debian or Ubuntu nodes using the puppet\_agent module, you must upgrade to version 1.6.2.

## Proxy support parameters added to puppet\_enterprise::pxp\_agent class \(2018.1.3\)

Parameters have been added to the class puppet\_enterprise::pxp\_agent to configure the proxy that connects to a master and pcp-broker. You can configure these parameters with a proxy URI such as `https://localhost:3128`.

-   master\_proxy: use to connect to to download task implementations.

-   broker\_proxy: use to connect to the pcp-broker to listen for task and Puppet runs.


## Environment option for backup and restore \(2018.1.3\)

You can now specify the environment to backup or restore using the `puppet-backup` command. By default, the `puppet-backup` command uses `--pe-environment=production`, so you must use this option to back up and restore if your master is assigned to a different environment than the default `production` environment.  

## `puppet infra configure` behavior \(2018.1.3\) 

This release includes the following enhancements to the way `puppet infra configure` affects `pe.conf`:

-   Tuning parameters added using Hiera or configuration data in the console are now saved to a file called `user_data.conf` when you either run `puppet infra configure` or upgrade. The `user_data.conf` file, which is saved in the same directory as the `pe.conf` file, remains in synch with your Hiera and configuration data settings. Previously, parameters were written to `pe.conf` and remained on your local system even if you removed the parameters from Hiera or configuration data.

-   Tuning parameters added to the `pe_repo` class now persist through upgrades. Previously, parameters were momentarily reverted during upgrades, and then changed back to the correct values. 

-   Commented-out lines in `pe.conf` now persist through `puppet infra configure` commands as well as upgrades. Previously, commented-out lines were removed when you upgraded or ran `puppet infra configure`.


## Simplified console admin password reset \(2018.1.3\)

This version simplifies resetting the console admin password such that you no longer have to explicitly call the puppet-agent ruby command. This changes the reset script from:

```
sudo /opt/puppetlabs/puppet/bin/ruby /opt/puppetlabs/server/bin/set_console_admin_password.rb <NEW_PASSWORD>
```

to:

```
sudo /opt/puppetlabs/server/bin/set_console_admin_password.rb <NEW_PASSWORD>
```

## Tabs added to Job list page \(2018.1.3\)

The Job list page uses tabs to organize jobs by type: **Puppet run**, **Task**, and **Plan**. These tabs replace the **Job type** filter.

**Tip:** A plan combines multiple tasks and runs them with a single Bolt command. For more information, see [Using Bolt with orchestrator](bolt_configure_orchestrator.md#).

## View permitted tasks from the command line \(2018.1.3\)

Run the command `puppet task show` to view a list of your permitted tasks. To view a list of all installed tasks pass the `--all` flag: `puppet task show --all`.

## Security \(2018.1.2\)

A Puppet Query Language \(PQL\) query has been added to the list of node options available for task permissions. When you assign role-based access to tasks, select **PQL query** to target nodes that meet specific conditions.

## JRuby \(2018.1.0\)

-   To support Ruby 2.3, this release changes the PE default setting for JRuby 9k to enabled \(`puppet_enterprise::master::puppetserver::jruby_9k_enabled: true`\). This default differs from open source Puppet and from previous versions of PE.

    **Important:** When upgrading to this version of PE, you must update any server-side installed gems or custom extensions to be compatible with Ruby 2.3 and JRuby 9k. For example, if you're using the autosign gem workflow, upgrade the gem to 0.1.3 and make sure you're not using yardoc 0.8.x. See [SERVER-2161](https://tickets.puppetlabs.com/browse/SERVER-2161) for details.

    To improve performance in installations with multiple JRuby instances or a limited `max_requests_per_instance`, increase the JVM code cache to 1G \(6-12 JRuby instances\) or 2G \(12-24 JRuby instances\), for example `puppet_enterprise::master::puppetserver::reserved_code_cache: 2g`.

    If you notice issues with JRuby in PE, [file a ticket](https://tickets.puppetlabs.com/secure/Dashboard.jspa) rather than changing the default `jruby_9k_enabled` parameter to avoid issues when this setting is eventually deprecated.


## Installation and upgrade \(2018.1.0\)

-   On Enterprise Linux systems, if you have a proxy between the agent and the master, you can now use the install script to specify an `http_proxy_host` to be used during package installation, for example `-s agent:http_proxy_host=<PROXY_FQDN>`. Previously, specifying a proxy host using the install script added the setting to `puppet.conf` without using it for installation.

-   Installer timestamps now include the offset from coordinated universal time \(UTC\) per ISO 8601 instead of the Java %date format previously used.
-   Updates to the Puppet Development Kit \(PDK\) let you test your modules for compatibility with PE before upgrading, and update or convert modules as needed.

## Preconfigured node groups \(2018.1.0\)

-   A new **PE Infrastructure** node group, **PE Database**, lets you set class parameters to control database configuration. Using the **PE Database** node group to specify parameters adds the new value to `pe.conf` and ensures that your settings persist through upgrades.


## Configuration parameters \(2018.1.0\)

-   The new `puppet_enterprise::profile::console::proxy::http_redirect::server_name` parameter lets you customize the target URL for HTTP redirects.
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

    **Tip:** Previous issues with the translation setting have been resolved, so you no longer need to set `environment_timeout` to `unlimited` if translation is enabled; however, doing so can speed catalog compilation.


## Orchestrator \(2018.1.0\)

-   You can now run Puppet or tasks from a node group in the console using the **Run** control. If there are no nodes in a group, the control is deactivated. If you have permission only to run Puppet, or only to run tasks, then the control changes to a button for the specific type of job you can run.


## Console \(2018.1.0\)

-   On a package's detail page, you can sort package instances by operating system version and environment.

-   Task metadata is available in the console. On the **Run a task** page, when you select a task to run, you can click **view task metadata** to see task and parameter information.

-   Enhanced behavior of sensitive parameters in tasks. If you mark a parameter as sensitive, its value will not be displayed in logs or API responses when the task runs. In addition, parameters marked as sensitive now appear with the value hidden, so you can rerun the job with the parameter.

-   Added conflict detection for node groups. When you commit changes to a node group, you are alerted if another user has made changes to the group while you were editing it. To support this enhancement, two keys have been added to the node classifier service API: [`serial-number`](groups_endpoint.md#entry-1521155801084) and [`last-edited`](groups_endpoint.md#entry-1521155793267).

-   Tasks that you run from Puppet Bolt as part of a plan appear in the **Job list** page, and you can filter them by the job type **Plan task**. You can view the job details for these tasks; however to rerun them you must use Bolt. \(A plan combines multiple tasks and runs them with a single command. For more information, see the docs for [Bolt](https://puppet.com/docs/bolt/0.x/bolt.html).\)

-   A sortable **Job ID** column appears in the run status table on the Overview page. The ID number of the job a node was most recently part of is displayed in this column.

-   On the **Permissions** tab in the User Rolespage, the user permissions object type has been changed from **Orchestrator** to **Job orchestrator** and its permissions from **View orchestrator** to **Start, stop and view jobs**. 

-   Puppet run metrics for each node are grouped and available under a **Metrics** tab. On the Reports page, click a node's **Report time**, and then click the **Metrics** tab.

-   Enhanced behavior of Event Inspector to work efficiently with large scale deployments. Deduplication of events was removed, and redesigned queries, double filtering, and pagination were added.


## Security \(2018.1.0\)

-   You can now revoke and reinstate access to PE for the Administrator account.

-   Access controls are hidden for users without permission to use them. Previously, unathorized users could see these controls but not use them.

-   Hostname and wildcard configuration options added to the RBAC directory services to validate that the certificate and the hostname for the connecting client match. To use, set `ssl_hostname_validation` \(defaults to `true`\), and `ssl_wildcard_validation` \(defaults to `false`\). For more information, see [Connecting external directory services to PE](rbac_ldap_intro.md#).


## Razor \(2018.1.0\)

-   The latest Razor client, version 1.8.1, removes the incompatibility with PE and standardizes the client \(`razor-client`\) for use with either PE or the open source Razor server.

-   Razor now includes built-in tasks for Ubuntu 16.04 and VMware ESXi 6.

-   The shiro.ini file used to enable authentication security now uses SHA-256 credential matching by default to specify password hashes.
-   A new `has_macaddress_like` tag operator can be used as a regular expression to match hardware MAC addresses.

-   New task template helpers `repo_file_contents (PATH)` and `repo_file? (PATH)` can be used to read and check the existence of repo files hosted on mirrors and created with the `--url` argument.
-   The `node` task template helper can now be used to evaluate whether a node booted via UEFI, for example `node.hw_hash['fact_boot_type'] == "efi"`.
-   This version of Razor documentation contains several improvements, including new details about ensuring scalability in your deployment, and clarifications to the API overview.

## Agent support \(2018.1.0\)

-   The agent release included in this version of  PE adds support for FIPS 140-2 compliant Enterprise Linux 7


## Internationalization \(2018.1.0\)

-   This version of PE changes the translation default to enabled \(`puppet_enterprise::master::disable_i18n: false`\), providing translated logs, reports, and some command-line interface text in Japanese. To see translated strings, your system locale and browser language must be set to Japanese, and for the text-based installer, you need [gettext](https://www.gnu.org/software/gettext/).

    **Tip:** Previous issues with the translation setting have been resolved, so you no longer need to set `environment_timeout` to `unlimited` if translation is enabled; however, doing so can speed catalog compilation.


## Support script \(2018.1.0\)

-   A new `--encrypt` flag can be used when running the support script to encrypt the resulting tarball with GnuPG encryption.

-   A new `--log-age` flag can be used when running the support script to specify how many days worth of logs to collect.


## Documentation \(2018.1.0\)

-   We've reorganized our Windows docs to align them with the rest of the general PE information. For the most part, interacting with Puppet is the same regardless of your operating system. The goals you have and tasks you do are the same. Where code and commands are different for Windows and \*nix, the documentation shows examples for both.

-   Work through the refreshed [Getting started on Windows](windows_getting_started_guide.md) section to get up and running with PE. If you are unfamiliar with \*nix-style system administration, see [Basic tasks and concepts in Windows](managing_windows_configurations.md#). For additional Windows-specific information, see [Troubleshooting Windows](troubleshooting_windows.md#), [Managing Windows configurations](managing_windows_configurations.md#), and [Installing and managing Windows modules](installing_and_using_windows_modules.md#).


**Related information**  


[Test modules before upgrade](upgrading_pe.md#)

[Infrastructure node groups](preconfigured_node_groups.md#)

[Customize the HTTPS redirect target URL](config_console.md#)

[Methods for configuring Puppet Enterprise](config_intro.md#)

