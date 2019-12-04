# Enhancements

These are the enhancements added to PE 2019.0.

## Infrastructure-only CRL disable option \(2019.0.4\)

You can now disable auto-creation of the infrastructure-only CRL, which was enabled by default in a previous release. To disable the option, set `puppet_enterprise::profile::certificate_authority::enable_infra_crl: false` in Hiera or `pe.conf`, or in the console, in the **PE Certificate Authority** node group, in the **puppet\_enterprise::profile::certificate\_authority** class, set the **enable\_infra\_crl** parameter to **false**.

## Specify alternate DNS names when regenerating certificates \(2019.0.4\)

When regenerating agent or master certificates, you can now pass an optional `dns_alt_names` parameter to the Bolt task.

## `puppet backup create` skips unnecessary database validation \(2019.0.3\)

The `puppet backup create` command now validates your PostgreSQL database only if you specify a `scope` of `puppetdb` or `config`. This change prevents errors if, for example, you specify only `scope=certs`, as you might in a split installation.

## Targeted replica reinitialization \(2019.0.3\)

In high availability installations, you can now target specific databases on the replica for reinitialization. Targeted reinitialization can fix replication errors that affect only a single database, rather than having to reinitialize the entire replica.

## Platform support \(2019.0.3\)

This version adds support for these platforms.

**Agent**

-   Enterprise Linux 8

-   macOS 10.14 Mojave


## Improved RBAC API log messages \(2019.0.3\)

The RBAC service log entries for revoked users attempting to log in now includes the username and UUID.

## Infrastructure-only CRL enabled by default \(2019.0.3\)

The functionality that creates an infrastructure-only CRL for the agents to download is now enabled by default.

## Preconfigured environment node groups \(2019.0.2\)

For new installations, we now package a more useful set of preconfigured environment node groups, including:

-   **Development environment**

    -   **Development one-time run exception**

-   **Production environment**

If you already have environment node groups set up, they are not affected by upgrading.

## Configuration settings preserved throughout upgrade \(2019.0.2\)

Configuration data that you've set in the console and Hiera is backed up every 30 minutes at `/etc/puppetlabs/enterprise/conf.d/user_data.conf` and restored when you upgrade.

## Infrastructure command for setting console administrator password \(2019.0.2\)

You can now set the console adminstrator password with `puppet infra console_password --password=<MY_PASSWORD>`. Use this command during text installs to avoid providing a plain text password in `pe.conf`, or use it to reset the password in an existing installation.

## Web-based installation no longer affects `known_hosts` file \(2019.0.2\)

The web-based installer now saves unrecognized SSH signatures in its own file, instead of in the user SSH `known_hosts` file. Previously, when you used the web-based installation method to install on a remote node that wasn't in the `known_hosts` file, the node's signature was added to that file. 

## Simplified upgrade with high availability enabled \(2019.0.2\)

For upgrades from 2017.3 and later with high availability enabled, you can now run just the upgrade script on your replica and reinitialize the replica.

## Simplified process for forgetting high availability replicas \(2019.0.2\)

The command to forget a high availability replica, `puppet infrastructure forget`, now includes steps to run Puppet on the master and purge the node as an agent.

## Installation data analytics, and a new opt-out method \(2019.0.2\)

We've added analytics data collection for the PE installer, and provided a new method for opting out of data collection during installation that works with all installation methods. You can opt out of data collection by setting the `DISABLE_ANALYTICS` environment variable when you run the installation script.  

## Console addition of `facts-blacklist-type` parameter \(2019.0.2\)

The `facts-blacklist-type` parameter is now a configurable parameter in the console. You can set this parameter to either `literal` or `regex`, as appropriate for the values in `facts-blacklist`.

## Regenerate master certificates without stopping the Puppet Server service \(2019.0.2\)

When regenerating master certificates in a monolithic installation, you no longer need to manually stop the Puppet Server service.

## Console rebranding \(2019.0.2\)

The color palette for Puppet Enterprise has been updated to align with company branding and adhere to W3C Web Content Accessibility Guidelines \(WCAG\) 2.0.

## Platform support \(2019.0.2\)

This version adds support for these platforms.

**Agent**

-   Fedora 29


## Shared tasks \(2019.0.1\)

You can now share code between tasks. A task can include a list of files that it requires, from any module, that it copies over and makes available via an `_installdir` parameter. This functionality also enables wrapping an existing script as a task. 

## Transport options for remotely run tasks \(2019.0.1\)

Multiple transport options are available for running tasks over the communication protocols SSH and WinRM. When you set up a task to run on nodes that do not have the Puppet agent installed, you can define additional target options such as port number, connection timeout duration, and temporary directory.

## Improved usability for agent installation from the console \(2019.0.1\)

Agent installation from the console has been enhanced to improve its usability. Now when you start agent installation, a link directs you to its corresponding job details page. And when the installation task completes, the list of unsigned certificates is updated with new targets.

## Setting console admin password during text mode installation \(2019.0.1\)

When installing using text mode, if you don't specify a console admin password in `pe.conf`, you're now prompted to specify the password during installation.

## Uninstall script on \*nix nodes \(2019.0.1\)

By default, the uninstall script is now copied to all \*nix nodes, including infrastructure nodes. You no longer have to manually copy the script from your master when you want to uninstall a \*nix node. Nodes installed on macOS are an exception not covered by the uninstall script. 

## Platform support \(2019.0.1\)

This version adds support for these platforms.

**Agent**

-   Windows Server 2019


## Certificate architecture and handling \(2019.0.0\)

PE, courtesy of Puppet Server, now uses an intermediate certificate authority architecture by default. This architecture enhances the security of your installation by injecting an additional layer of certification between your root certificate authority and agent nodes. If you're upgrading to this version of PE, you can optionally adopt the intermediate certificate architecture.

To adopt the new CA architecture, both your master and agents must be upgraded, and you must [regenerate certificates](regenerate_certificates.md#). You can use pre-6.x agents with a Puppet 6.x or PE 2019.0 or later master, but this combination doesn't take advantage of the new intermediate certificate authority architecture. If you don't upgrade *all *of your nodes to 6.x, don't regenerate certificates, because pre-6.x agents won't work with the new CA architecture. 

Additionally, the `puppet cert` subcommands that formerly controlled certificates have been replaced with `puppetserver ca` commands, which leverage a new Puppet Server CA API and client tool. The new API allows for cleaner handling of certificates and, because the client tool is delivered as a gem alonside Puppet Server, it can be updated out-of-band to incorporate improvements or bug fixes.

For details about the new certificate architecture and client tool see the Puppet Server 6.0 documentation about the [CA subcommand](https://puppet.com/docs/puppetserver/6.0/subcommands.html#ca), [intermediate CA](https://puppet.com/docs/puppetserver/6.0/intermediate_ca.html), [certificate authority service](https://puppet.com/docs/puppetserver/6.0/services_master_puppetserver.html#certificate-authority-service), and [ca.conf](https://puppet.com/docs/puppetserver/6.0/config_file_ca.html). For details about the CA API, see the Puppet 6.0 [HTTP API](https://puppet.com/docs/puppet/6.0/http_api/http_api_index.html#puppet-and-puppet-ca-apis) docs.

## Shared task executables \(2019.0.0\)

Multiple task implementations can refer to the same executable file.

## \*nix bulk plugin sync with the install script \(2019.0.0\)

For \*nix agents, the agent install script now downloads a tarball of plugins from the master before the agent runs for the first time. Depending on how many modules you have installed, bulk plugin sync can speed agent installation significantly.

**Note:** If your master runs in a different environment from your agent nodes, you might see some reduced benefit from bulk plugin sync. The plugin tarball is created based on the plugins running on the master agent, which might not match the plugins required for agents in a different environment.

This feature is controlled by the setting `pe_repo::enable_bulk_pluginsync` which you can configure in Hiera or in the console. Bulk plugin-sync is set to `true` \(enabled\) by default.  

## `pe.conf` synched to high availability replicas \(2019.0.0\) 

The `enterprise/conf.d` directory, including the `pe.conf` file, is now automatically synched from masters to replicas in high availability installations. This expands the available methods for specifying configuration parameters in HA configurations. Previously, we recommended using only Hiera to specify configuration parameters. With this addition, you can now use Hiera or `pe.conf`.  

## Platform support \(2019.0.0\)

This version adds support for these platforms.

**Agent**

-   SUSE Linux Enterprise Server 15


