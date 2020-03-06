# Getting support

You can get commercial support for versions of PE in mainstream and extended support. You can also get support from our user community.

## Puppet Enterprise support life cycle

Some of our releases have long term support \(LTS\); others have short term support \(STS\). For each release, there are three phases of product support: Mainstream support, Extended support, and End of Life \(EOL\).

For full information about types of releases, support phases and dates for each release, frequency of releases, and recommendations for upgrading, see the [Puppet Enterprise support lifecycle](https://puppet.com/misc/puppet-enterprise-lifecycle) page.

If the latest release with the most up-to-date features is right for you, [Download, try, or upgrade Puppet Enterprise](https://puppet.com/download-puppet-enterprise). Alternatively, download an older supported release from [Previous releases](https://puppet.com/misc/pe-files/previous-releases).

### Open source tools and libraries

PE uses open source tools and libraries. We use both externally maintained components \(such as Ruby, PostgreSQL, and the JVM\) and also projects which we own and maintain \(such as Facter, Puppet agent, Puppet Server, and PuppetDB.\)

Projects which we own and maintain are "upstream" of our commercial releases. Our open source projects move faster and have shorter support life cycles than PE. We might discontinue updates to our open source platform components before their commercial EOL dates. We vet upstream security and feature releases and update supported versions according to customer demand and our [Security policy](https://puppet.com/docs/vulnerability-submission-process).

## The customer support portal

We provide responsive, dependable, quality support to resolve any issues regarding the installation, operation, and use of Puppet Enterprise \(PE\).

There are two levels of commercial support plans for PE: Standard and Premium. Both allow you to report your support issues to our confidential [customer support portal](https://support.puppet.com/hc/). When you purchase PE, you receive an account and log-on for the portal, which includes access to our knowledge base.

**Note:** We use the term "standard installation" to refer to a PE installation of up to 4,000 nodes. A Standard Support Plan is not limited to this installation type, however. Standard here refers to the support level, not the size of the PE installation.

### Puppet Enterprise support script

When seeking support, you might be asked to run an information-gathering support script. This script collects a large amount of system information and PE diagnostics, compresses the data, and prints the location of the zipped tarball when it finishes running.

The script is provided by the `pe_support_script` module bundled with the installer.

#### Running the support script

Run the support script on the command line of your PE master or any agent node running Red Hat Enterprise Linux, Ubuntu, or SUSE Linux Enterprise Server operating systems with the command: `/opt/puppetlabs/bin/puppet enterprise support`.

Use these options when you run the support script to modify the output:

|Option|Description|
|------|-----------|
|`--verbose`|Logs verbosely.|
|`--debug`|Logs debug information.|
|`--classifier`|Collects classification data.|
|`--dir <DIRECTORY>`|Specifies where to save the support script's resulting tarball.|
|`--ticket <NUMBER>`|Specifies a support ticket number for record-keeping purposes.|
|`--encrypt`|Encrypts the support script's resulting tarball with GnuPG encryption. **Note:** You must have GPG or GPG2 available in your `PATH` in order to encrypt the tarball.

|
|`--log-age`|Specifies how many days worth of logs the support script collects. Valid values are positive integers or `all` to collect all logs, up to 1 GB per log.|

The next iteration of the support script, version 3, is currently under development and may be selected by running `/opt/puppetlabs/bin/puppet enterprise support --v3`. This new version of the support script has more options, which can be used in addition to the list in the table above.

|Option|Description|
|------|-----------|
|`--v3`|Activate version 3 of the support script. This option is required in order to use any other option listed in this table.|
|`--list`|List diagnostics that may be enabled or disabled. Diagnostics labeled "opt-in" must be explicitly enabled. All others are enabled by default.|
|`--enable <LIST>`|A comma-separated list of diagnostic names to enable. Use the `--list` option to print available names. The `--enable` option must be used to activate diagnostics marked as "opt-in."|
|`--disable <LIST>`|A comma-separated list of diagnostic names to enable. Use the `--list` option to print available names.|
|`--only <LIST>`|A comma-separated list of diagnostic names to enable. All other diagnostics will be disabled. Use the `--list` option to print available names.|
|`--upload`|Upload the output tarball to Puppet Support via SFTP. Requires the `--ticket <NUMBER>` option to be used.|
|`--upload_disable_host_key_check`|Disable SFTP host key checking. See [Support article KB\#0305](https://support.puppet.com/hc/en-us/articles/360009970114-KB-0305-How-do-I-use-SFTP-to-upload-files-to-Puppet-support-) for a list of current host key values.|
|`--upload_user <USER>`|Specify a SFTP user to use when uploading. If not specified, a shared write-only account will be used.|
|`--upload_key <FILE>`|Specify a SFTP key to use with `--upload_user`.|

Here are some examples of using the version 3 flags when running the support script:

```
# Collect diagnostics for just Puppet and Puppet Server
/opt/puppetlabs/bin/puppet enterprise support --v3 --only puppet,puppetserver

# Enable collection of PE classification
/opt/puppetlabs/bin/puppet enterprise support --v3 --enable pe.console.classifier-groups

# Disable collection of system logs, upload result to Puppet
/opt/puppetlabs/bin/puppet enterprise support --v3 --disable system.logs --upload --ticket 12345
```

Descriptions of diagnostics that can be selected using the `--enable`, `--disable`, and `--only` flags are found in the next section.

#### Information collected by the support script

The following sections describe the information collected by version 1 and version 3 of the support script.

**base-status**

The `base-status` check collects basic diagnostics about the PE installation. This check is unique in that it is always enabled and is not affected by the `--disable` or `--only` flags.

Information collected by the `base-status` check:

-   The version of the support script that is running, the Puppet ticket number, if supplied, and the time at which the script was run.


**system**

The checks in the `system` scope gather diagnostics, logs, and configuration related to the operating system.

Information collected by the `system.config` check:

-   A copy of `/etc/hosts`

-   A copy of `/etc/nsswitch.conf`

-   A copy of `/etc/resolv.conf`

-   Configuration for the apt, yum, and dnf package managers

-   The operating system version

-   The umask in effect

-   The status of SELinux

-   A list of configured network interfaces

-   A list of configured firewall rules

-   A list of loaded firewall kernel modules


Information collected by the `system.logs` check:

-   A copy of the system log \(syslog\)

-   A copy of the kernel log \(dmesg\)


Information collected by the `system.status` check:

-   Values of variables set in the environment

-   A list of running processes

-   A list of enabled services

-   The uptime of the system

-   A list of established network connections

-   NTP status

-   The IP address and hostname of the node running the script, according to DNS

-   Disk usage

-   RAM usage


**puppet**

The checks in the `puppet` scope gather diagnostics, logs, and configuration related to the Puppet agent services.

Information collected by the `puppet.config` check:

-   Facter configuration files:

-   `/etc/puppetlabs/facter/facter.conf`
-   Puppet configuration files:

-   `/etc/puppetlabs/puppet/device.conf`
-   `/etc/puppetlabs/puppet/hiera.yaml`

-   `/etc/puppetlabs/puppet/puppet.conf`
-   PXP agent configuration files:

-   `/etc/puppetlabs/pxp-agent/modules/`
-   `/etc/puppetlabs/pxp-agent/pxp-agent.conf`

Information collected by the `puppet.logs` check:

-   Puppet log files:
    -   `/var/log/puppetlabs/puppet`
-   JournalD logs for the `puppet` service
-   PXP agent log files:
    -   `/var/log/puppetlabs/puppet`
-   JournalD logs for the `pxp-agent` service

Information collected by the `puppet.status` check:

-   `facter -p` output and debug-level messages
-   A list of Ruby gems installed for use by Puppet
-   Ping output for the Puppet Server the agent is configured to use
-   A copy of the following files and directories from the Puppet `statedir`:
    -   `classes.txt`
    -   `graphs/`
    -   `last_run_summary.yaml`
-   A listing of metadata \(name, size, etc.\) of files present in the following directories:
    -   `/etc/puppetlabs`
    -   `/var/log/puppetlabs`
    -   `/opt/puppetlabs`
-   A listing of Puppet and PE packages installed on the system along with verification output for each

**puppetserver**

The checks in the `puppetserver` scope gather diagnostics, logs, and configuration related to the Puppet Server service.

Information collected by the `puppetserver.config` check:

-   Puppet Server configuration files:
    -   `/etc/puppetlabs/code/hiera.yaml`
    -   `/etc/puppetlabs/puppet/auth.conf`
    -   `/etc/puppetlabs/puppet/autosign.conf`
    -   `/etc/puppetlabs/puppet/classfier.yaml`
    -   `/etc/puppetlabs/puppet/fileserver.conf`
    -   `/etc/puppetlabs/puppet/hiera.yaml`
    -   `/etc/puppetlabs/puppet/puppet.conf`
    -   `/etc/puppetlabs/puppet/puppetdb.conf`
    -   `/etc/puppetlabs/puppet/routes.yaml`
    -   `/etc/puppetlabs/puppetserver/bootstrap.cfg`
    -   `/etc/puppetlabs/puppetserver/code-manager-request-logging.xml`
    -   `/etc/puppetlabs/puppetserver/conf.d/`
    -   `/etc/puppetlabs/puppetserver/logback.xml`
    -   `/etc/puppetlabs/puppetserver/request-logging.xml`
    -   `/etc/puppetlabs/r10k/r10k.yaml`
    -   `/opt/puppetlabs/server/data/code-manager/r10k.yaml`

Information collected by the `puppetserver.logs` check:

-   Puppet Server log files:
    -   `/var/log/puppetlabs/puppetserver/`
-   JournalD logs for the `pe-puppetserver` service
-   r10k log files:
    -   `/var/log/puppetlabs/r10k/`

Information collected by the `puppetserver.metrics` check:

-   Data stored in `/opt/puppetlabs/puppet-metrics-collector/puppetserver`

Information collected by the `puppetserver.status` check:

-   A list of certificates issued by the Puppet CA

-   A list of Ruby gems installed for use by Puppet Server

-   Output from the `status/v1/services` API

-   Output from the `puppet/v3/environment_modules` API

-   Output from the `puppet/v3/environments` API

-   `environment.conf` and `hiera.yaml` files from each Puppet code environment

-   The disk space used by Code Manager cache, storage, client, and staging directories

-   The disk space used by the server's File Bucket

-   The output of `r10k deploy display`

**puppetdb**

The checks in the `puppetdb` scope gather diagnostics, logs, and configuration related to the PuppetDB service.

Information collected by the `puppetdb.config` check:

-   Configuration files:
    -   `/etc/puppetlabs/puppetdb/bootstrap.cfg`
    -   `/etc/puppetlabs/puppetdb/certificate-whitelist`
    -   `/etc/puppetlabs/puppetdb/conf.d/`
    -   `/etc/puppetlabs/puppetdb/logback.xml`
    -   `/etc/puppetlabs/puppetdb/request-logging.xml`

Information collected by the `puppetdb.logs` check:

-   PuppetDB log files: `/var/log/puppetlabs/puppetdb`
-   JournalD logs for the `pe-puppetdb` service

Information collected by the `puppetdb.metrics` check:

-   Data stored in `/opt/puppetlabs/puppet-metrics-collector/puppetdb`

Information collected by the `puppetdb.status` check:

-   Output from the `status/v1/services` API
-   Output from the `pdb/admin/v1/summary-stats` API
-   A list of active certnames from the PQL query `nodes[certname] {deactivated is null and expired is null}`

**pe**

The checks in the `pe` scope gather diagnostics, logs, and configuration related to Puppet Enterprise services.

Information collected by the `pe.config` check:

-   Installer configuration files:
    -   `/etc/puppetlabs/enterprise/conf.d/`
    -   `/etc/puppetlabs/enterprise/hiera.yaml`
    -   `/etc/puppetlabs/installer/answers.install`
-   PE client tools configuration files:
    -   `/etc/puppetlabs/client-tools/orchestrator.conf`
    -   `/etc/puppetlabs/client-tools/puppet-access.conf`
    -   `/etc/puppetlabs/client-tools/puppet-code.conf`
    -   `/etc/puppetlabs/client-tools/puppetdb.conf`
    -   `/etc/puppetlabs/client-tools/services.conf`

Information collected by the `pe.logs` check:

-   PE installer log files:
    -   `/var/log/puppetlabs/installer/`

-   PE backup and restore log files:
    -   `/var/log/puppetlabs/pe-backup-tools/`
    -   `/var/log/puppetlabs/puppet_infra_recover_config_cron.log`

Information collected by the `pe.status` check:

-   Output from `puppet infra status`
-   Current tuning settings from `puppet infra tune`
-   Recommended tuning settings from `puppet infra tune`

This check is disabled by default. Information collected by the `pe.file-sync` check when activated by the `--enable` option:

-   Puppet manifests and other content from `/etc/puppetlabs/code-staging/`
-   Puppet manifests and other content stored in Git repos under `/opt/puppetlabs/server/data/puppetserver/filesync`

**pe.console**

The checks in the `pe.console` scope gather diagnostics, logs, and configuration related to the Puppet Enterprise console service.

Information collected by the `pe.console.config` check:

-   Configuration files:
    -   `/etc/puppetlabs/console-services/bootstrap.cfg`
    -   `/etc/puppetlabs/console-services/conf.d/`
    -   `/etc/puppetlabs/console-services/logback.xml`
    -   `/etc/puppetlabs/console-services/rbac-certificate-whitelist`
    -   `/etc/puppetlabs/console-services/request-logging.xml`
    -   `/etc/puppetlabs/nginx/conf.d/`
    -   `/etc/puppetlabs/nginx/nginx.conf`

Information collected by the `pe.console.logs` check:

-   Console log files:
    -   `/var/log/puppetlabs/console-services/`
    -   `/var/log/puppetlabs/nginx/`
-   JournalD logs for the `pe-puppetdb` and `pe-nginx` services

Information collected by the `pe.console.status` check:

-   Output from the `/status/v1/services` API
-   Directory service connection configuration, with passwords removed

This check is disabled by default. Information collected by the `pe.console.classifier-groups` check when activated by the `--enable` option:

-   All classification data provided by the `/v1/groups` API endpoint

**pe.orchestration**

The checks in the `pe.orchestration` scope gather diagnostics, logs, and configuration related to the Puppet Enterprise orchestration services.

Information collected by the `pe.orchestration.config` check:

-   ACE server configuration files:
    -   `/etc/puppetlabs/puppet/ace-server/conf.d/`
-   Bolt server configuration files:
    -   `/etc/puppetlabs/puppet/bolt-server/conf.d/`
-   Orchestration service configuration files:
    -   `/etc/puppetlabs/puppet/orchestration-services/bootstrap.cfg`
    -   `/etc/puppetlabs/puppet/orchestration-services/conf.d/analytics.conf`
    -   `/etc/puppetlabs/puppet/orchestration-services/conf.d/auth.conf`
    -   `/etc/puppetlabs/puppet/orchestration-services/conf.d/global.conf`
    -   `/etc/puppetlabs/puppet/orchestration-services/conf.d/inventory.conf`
    -   `/etc/puppetlabs/puppet/orchestration-services/conf.d/metrics.conf`
    -   `/etc/puppetlabs/puppet/orchestration-services/conf.d/orchestrator.conf`
    -   `/etc/puppetlabs/puppet/orchestration-services/conf.d/pcp-broker.conf`
    -   `/etc/puppetlabs/puppet/orchestration-services/conf.d/web-routes.conf`
    -   `/etc/puppetlabs/puppet/orchestration-services/conf.d/webserver.conf`
    -   `/etc/puppetlabs/puppet/orchestration-services/logback.xml`
    -   `/etc/puppetlabs/puppet/orchestration-services/request-logging.xml`

Information collected by the `pe.orchestration.logs` check:

-   ACE server log files: `/var/log/puppetlabs/ace-server/`
-   JournalD logs for the `pe-ace-server` service
-   Bolt server log files: `/var/log/puppetlabs/bolt-server/`
-   JournalD logs for the `pe-bolt-server` service
-   Orchestrator log files: `/var/log/puppetlabs/orchestration-services/`
-   JournalD logs for the `pe-orchestration-services` service

Information collected by the `pe.orchestration.metrics` check:

-   Data stored in `/opt/puppetlabs/puppet-metrics-collector/orchestrator/`

Information collected by the `pe.orchestration.status` check:

-   Output from the `/status/v1/services` API

**pe.postgres**

The checks in the `pe.postgres` scope gather diagnostics, logs, and configuration related to the Puppet Enterprise PostgreSQL database.

Information collected by the `pe.postgres.config` check:

-   Configuration files:
    -   `/opt/puppetlabs/server/data/postgresql/*/data/postgresql.conf`
    -   `/opt/puppetlabs/server/data/postgresql/*/data/postmaster.opts`
    -   `/opt/puppetlabs/server/data/postgresql/*/data/pg_ident.conf`
    -   `/opt/puppetlabs/server/data/postgresql/*/data/pg_hba.conf`

Information collected by the `pe.postgres.logs` check:

-   PostgreSQL log files:
    -   `/var/log/puppetlabs/postgresql/*/`
    -   `/opt/puppetlabs/server/data/postgresql/pg_upgrade_internal.log`
    -   `/opt/puppetlabs/server/data/postgresql/pg_upgrade_server.log`
    -   `/opt/puppetlabs/server/data/postgresql/pg_upgrade_utility.log`
-   JournalD logs for the `pe-postgresql` service

Information collected by the `pe.postgres.status` check:

-   A list of setting values that the database is using while running
-   A list of currently established database connections and the queries being executed
-   A distribution of Puppet run start times for thundering herd detection
-   The status of any configured replication slots
-   The status of any active replication connections
-   The size of database directories on disk
-   The size of databases as reported by the database service
-   The size of tables and indices within databases

## Getting support from the Puppet community

As a Puppet Enterprise customer you are more than welcome to participate in our large and helpful open source community as well as report issues against the open source project.

-   Join the [Puppet Enterprise user group](https://groups.google.com/a/puppet.com/forum/#!forum/pe-users). Your request to join is sent to Puppet, Inc. for authorization and you receive an email when you’ve been added to the user group.
    -   Click on “Sign in and apply for membership.”
    -   Click on “Enter your email address to access the document.”
    -   Enter your email address.
-   Join the open source [Puppet user group](http://groups.google.com/group/puppet-users).
-   Join the [Puppet developers group](http://groups.google.com/group/puppet-dev).
-   Report issues with the [open source Puppet project](https://tickets.puppetlabs.com/browse/PUP).

