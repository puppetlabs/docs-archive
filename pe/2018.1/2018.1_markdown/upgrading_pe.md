# Upgrading Puppet Enterprise

Upgrade your PE installation as new versions become available.

## Upgrade paths

These are the valid upgrade paths for PE.

|If you're on version...|Upgrade to...|Notes|
|-----------------------|-------------|-----|
|2018.1.z|You're up to date!| |
|2017.3.z|2018.1| |
|2017.2.z|2018.1| |
|2017.1.z|2018.1| |
|2016.5.z|2018.1| |
|2016.4.10 or later|2018.1| |
|2016.4.9 or earlier|latest 2016.4.z, then 2018.1|To upgrade to 2018.1 from 2015.2.z through 2016.4.9, you must first [upgrade to the latest 2016.4.z](https://docs.puppet.com/pe/2016.4/upgrade_mono.html).|
|2016.2.z|
|2016.1.z|
|2015.3.z|
|2015.2.z|
|3.8.x|latest 2016.4.z, then 2018.1|To upgrade from 3.8.x, you must first [migrate to the latest 2016.4.z](https://docs.puppet.com/pe/2016.4/migrate_pe_catalog_preview.html). This upgrade requires a different process than upgrades from other versions.|

## Upgrade cautions

These are the major updates to recent PE versions to be aware of when upgrading.

**Important:** Always back up your installation before performing any upgrade.

### PostgreSQL upgrade in PE 2017.3

PE 2017.3 and later uses PostgreSQL 9.6.

-   Before upgrading, make sure you have at least 1.1 times the space used by the existing `/opt/puppetlabs/server/data/postgresql` directory. Plan for a downtime window of a couple of hours if you have a large database, and don't worry if your upgrade process seems to hang while upgrading the database—it's not hung.

-   After upgrading, verify that your installation is working as expected \(log into the console and check for historical reports and custom classification groups\), then free disk space by cleaning up these PostgreSQL 9.4 directories:

    -   `/opt/puppetlabs/server/data/postgresql/9.4`
    -   `/opt/puppetlabs/server/data/postgresql/activity/PG_9.4*`
    -   `/opt/puppetlabs/server/data/postgresql/classifier/PG_9.4*`
    -   `/opt/puppetlabs/server/data/postgresql/orchestrator/PG_9.4*`
    -   `/opt/puppetlabs/server/data/postgresql/puppetdb/PG_9.4*`
    -   `/opt/puppetlabs/server/data/postgresql/rbac/PG_9.4*`
    **Important:** Don't remove any directories that start with `PG_9.6`.

-   If you use **external PostgreSQL**, you must take extra steps to upgrade. For details, see [Upgrading PostgreSQL](upgrading_pe.md#).

-   If you're upgrading with **high availability enabled**, upgrade and then forget the existing replica, and provision and enable a new replica. For details, see [Upgrade with high availability enabled](upgrading_pe.md#).


### JRuby 9k upgrade in PE 2018.1

To support Ruby 2.3, PE 2018.1 and later changes the default setting for JRuby 9k to enabled \(`puppet_enterprise::master::puppetserver::jruby_9k_enabled: true`\). This default differs from open source Puppet and from previous versions of PE.

-   After upgrading, update any server-side installed gems or custom extensions to be compatible with Ruby 2.3 and JRuby 9k. For example, if you're using the autosign gem workflow, upgrade the gem to 0.1.3 and make sure you're not using yardoc 0.8.x. See [SERVER-2161](https://tickets.puppetlabs.com/browse/SERVER-2161) for details.

-   For information on enabling or disabling autosigning, see [Autosigning certificate requests](https://puppet.com/docs/puppet/5.5/ssl_autosign.html).

-   If you notice issues with JRuby in PE, [file a ticket](https://tickets.puppetlabs.com/secure/Dashboard.jspa) rather than changing the default parameter to avoid issues when this setting is eventually deprecated.


### MCollective deprecation in PE 2018.1

PE 2018.1 and later no longer installs MCollective by default. If you have an existing PE installation that relies on MCollective and you upgrade by installing the new version and then moving agents over, you must enable MCollective when installing 2018.1, prior to migrating agents.

**Related information**  


[Backing up and restoring Puppet Enterprise](backing_up_and_restoring_pe.md)

[Setting up MCollective](mcollective_overview.md#)

## Test modules before upgrade

To ensure that your modules will work with the newest version of PE, update and test them with Puppet Development Kit \(PDK\) before upgrading.

### Before you begin

If you are already using PDK, your modules should pass validation and unit tests with your currently installed version of PDK. 

### About this task

Update PDK with each new release to ensure compatability with new versions of PE.

### Procedure

1.  Download and install PDK. If you already have PDK installed, this updates PDK to its latest version. For detailed instructions and download links, see the [installing](https://puppet.com/docs/pdk/1.x/pdk_install.html) instructions.

2.  If you have not previously used PDK with your modules, convert them to a PDK compatible format. This makes changes to your module to enable validation and unit testing with PDK. For important usage details, see the [converting modules](https://puppet.com/docs/pdk/1.x/pdk_converting_modules.html) documentation. 

    For example, from within the module directory, run:

    ```
    pdk convert
    ```

3.  If your modules are already compatible with PDK, update them to the latest module template. If you converted modules in step 2, you do not need to update the template. To learn more about updating, see the [updating module](https://puppet.com/docs/pdk/1.x/pdk_updating_modules.html) templates documentation.

    For example, from within the module directory, run:

    ```
    pdk update
    ```

4.  Validate and run unit tests for each module, specifying the version of PE you are upgrading to. When specifying a PE version, be sure to specify at least the year and the release number, such as 2018.1. For information about module validations and testing, see the [validating and testing modules](https://puppet.com/docs/pdk/1.x/pdk_testing.html) documentation.

    For example, from within the module directory, run:

    ```
    pdk validate
    pdk test unit
    ```

    The `pdk test unit` command verifies that testing dependencies and directories are present and runs the unit tests that you write. It does not create unit tests for your module.

5.  If your module fails validation or unit tests, make any necessary changes to your code.


### Results

After you've verified that your modules work with the new PE version, you can continue with your upgrade.

## Upgrade a monolithic installation

To upgrade a monolithic installation, run the text-based PE installer on your master or master of masters, and then upgrade any additional components.

### Before you begin

Back up your PE installation.

### About this task

If you encounter errors during upgrade, you can fix them and run the installer again.

### Procedure

1.  [Download](https://puppet.com/try-puppet/puppet-enterprise/download/) the tarball appropriate to your operating system and architecture.

2.  Unpack the installation tarball: `tar -xf <tarball>`

    You need about 1 GB of space to untar the installer.

3.  From the installer directory, run the installer: `sudo ./puppet-enterprise-installer`

    **Note:** To specify a different `pe.conf` file other than the existing file, use the -c flag:

    ```
    sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>
    ```

    With this flag, your previous `pe.conf` is backed up to `/etc/puppetlabs/enterprise/conf.d/<TIMESTAMP>.conf` and a new `pe.conf` is created at `/etc/puppetlabs/enterprise/conf.d/pe.conf`.

4.  If you have compile masters, upgrade them.

    SSH into each compile master and run:

    ```
    /opt/puppetlabs/puppet/bin/curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<PUPPET MASTER FQDN>:8140/packages/current/upgrade.bash | sudo bash
    ```

5.  If you have ActiveMQ hubs and spokes, upgrade them.

    SSH into each hub and spoke and run:

    ```
    /opt/puppetlabs/puppet/bin/curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<PUPPET MASTER FQDN>:8140/packages/current/upgrade.bash | sudo bash
    ```

6.  Upgrade these additional PE infrastructure components.

    -   Agents

    -   Razor

    -   PE client tools — Install the appropriate version of client tools that matches the PE version you upgraded to.


**Related information**  


[Backing up and restoring Puppet Enterprise](backing_up_and_restoring_pe.md)

## Upgrade with high availability enabled

If you have high availability enabled, your upgrade path differs depending on what version you're upgrading from. Regardless of upgrade method, the replica is temporarily unavailable to serve as backup during this process, so you should time your upgrade to minimize risk.

### Before you begin

Back up your PE installation.

**Related information**  


[Backing up and restoring Puppet Enterprise](backing_up_and_restoring_pe.md)

### Upgrade from pre-2017.3 with high availability enabled

Upgrading from PE versions earlier than 2017.3 requires forgetting and re-creating your replica in order to update to the latest PostgreSQL version.

#### Procedure

1.  Upgrade PE.

2.  Forget your existing replica.

3.  Provision a new replica.

4.  Enable the new replica.


**Related information**  


[Forget a replica](configure_high_availability.md#)

[Provision a replica](configure_high_availability.md#)

[Enable a replica](configure_high_availability.md#)

### Upgrade from 2017.3 or later with high availability enabled

Upgrading from 2017.3 or later requires upgrading and reinitializing your replica.

#### About this task

#### Procedure

1.  Upgrade PE.

2.  On your replica, run the upgrade script:

    `curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<MASTER_HOST>:8140/packages/current/upgrade.bash | bash`

3.  Verify that master and replica services are operational:

    ```
    /opt/puppetlabs/bin/puppet-infra status
    ```

4.  If your replica reports errors, reinitialize the replica:

    `/opt/puppetlabs/bin/puppet-infra reinitialize replica -y`


## Upgrade a split or large environment installation

To upgrade a split or large environment installation, run the text-based installer on each infrastructure node in your environment, and then upgrade any additional components.

### Before you begin

Back up your PE installation.

### About this task

**Note:** You must upgrade the components in the order specified.

If you encounter errors during upgrade, you can fix them and run the installer again.

**Related information**  


[Backing up and restoring Puppet Enterprise](backing_up_and_restoring_pe.md)

### Upgrade the master

Upgrading the master is the first step in upgrading a split or large environment installation.

#### About this task

#### Procedure

1.  [Download](https://puppet.com/try-puppet/puppet-enterprise/download/) the tarball appropriate to your operating system and architecture.

2.  Unpack the installation tarball: `tar -xf <tarball>`

    You need about 1 GB of space to untar the installer.

3.  Stop the agent service: `sudo puppet resource service puppet ensure=stopped`

4.  From the installer directory, run the installer: `sudo ./puppet-enterprise-installer`

    **Note:** To specify a different `pe.conf` file other than the existing file, use the -c flag:

    ```
    sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>
    ```

    With this flag, your previous `pe.conf` is backed up to `/etc/puppetlabs/enterprise/conf.d/<TIMESTAMP>.conf` and a new `pe.conf` is created at `/etc/puppetlabs/enterprise/conf.d/pe.conf`.

5.  When upgrade completes, transfer the installer and the `pe.conf` file located at `/etc/puppetlabs/enterprise/conf.d/` to the next server that you're upgrading a component on.


### Upgrade PuppetDB

In a split installation, after you upgrade the master, you're ready to upgrade PuppetDB.

#### About this task

#### Procedure

1.  Unpack the installation tarball: `tar -xf <tarball>`

    You need about 1 GB of space to untar the installer.

2.  Stop the agent service: `sudo puppet resource service puppet ensure=stopped`

3.  From the installer directory, run the installer: `sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>`

4.  When upgrade completes, transfer the installer and the `pe.conf` file located at `/etc/puppetlabs/enterprise/conf.d/` to the next server that you're upgrading a component on.


### Upgrade the console

In a split installation, after you upgrade the master and PuppetDB, you're ready to upgrade the console.

#### About this task

#### Procedure

1.  Unpack the installation tarball: `tar -xf <tarball>`

    You need about 1 GB of space to untar the installer.

2.  Stop the agent service: `sudo puppet resource service puppet ensure=stopped`

3.  From the installer directory, run the installer: `sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>`


### Run Puppet on infrastructure nodes

To complete a split upgrade, run Puppet on all infrastructure nodes in the order that they were upgraded.

#### About this task

#### Procedure

1.  Run Puppet on the master node.

2.  Run Puppet on the PuppetDB node.

3.  Run Puppet on the console node.


### Upgrade remaining infrastructure nodes

After the main components of your infrastructure are upgraded, you must upgrade any additional infrastructure nodes, such as compile masters, hubs, and spokes.

#### Procedure

1.  If you have compile masters, upgrade them.

    SSH into each compile master and run:

    ```
    /opt/puppetlabs/puppet/bin/curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<PUPPET MASTER FQDN>:8140/packages/current/upgrade.bash | sudo bash
    ```

2.  If you have ActiveMQ hubs and spokes, upgrade them.

    SSH into each hub and spoke and run:

    ```
    /opt/puppetlabs/puppet/bin/curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<PUPPET MASTER FQDN>:8140/packages/current/upgrade.bash | sudo bash
    ```

3.  Upgrade these additional PE infrastructure components.

    -   Agents

    -   Razor

    -   PE client tools — Install the appropriate version of client tools that matches the PE version you upgraded to.


## Migrate from a split to a monolithic installation

Split installations, where the master, console, and PuppetDB are installed on separate nodes, are deprecated. Migrate from an existing split installation to a monolithic installation—with or without compilers—and a standalone PE-PostgreSQL node.

### Before you begin

You must be running a version of PE on all infrastructure nodes that includes the `puppet infrastructure run` command. To verify that this command is available on your systems, run `puppet infrastructure run --help`.

The `puppet infrastructure run` command leverages built-in Bolt plans to automate certain management tasks. To use this command, you must be able to connect using SSH from your master to any nodes that the command modifies. You can establish an SSH connection using key forwarding, a local key file, or by specifying keys in `.ssh/config` on your master. For more information, see [Bolt OpenSSH configuration options](https://puppet.com/docs/bolt/latest/bolt_configuration_options.html#openssh-configuration-options).

### About this task

To view all available parameters, use the `--help` flag. The logs for all `puppet infrastructure run` Bolt plans are located at `/var/log/puppetlabs/installer/bolt_info.log`.

**Important:** The migration command used in this task automates a number of manual steps, including editing `pe.conf`, unpinning and uninstalling packages from affected infrastructure nodes, and running Puppet multiple times. Treat this process as you would any major migration by thoroughly testing it in an environment that's as similar to your production environment as possible.

### Procedure

1.  On your master, verify that `pe.conf` contains correct information for `console_host`, `puppetdb_host`, and `database_host`.

    The migration command uses this information to correctly migrate these nodes.

    **Note:** If your split PE installation includes multiple standalone PuppetDB nodes, the migration command will fail with an error.

2.  Make sure that the master can connect via SSH to your console node, PuppetDB node, and \(if present\) standalone PE-PostgreSQL node.

3.  On your master logged in as root, run `puppet infrastructure run migrate_split_to_mono`

    You can specify this optional parameter:

    -   `tmpdir` — Path to a directory to use for uploading and executing temporary files.

### Results

After completion, your master is running the console and PuppetDB services and you can retire or repurpose the old console node. If you **did not** start with a standalone PE-PostgreSQL node, your old PuppetDB node now functions in that capacity. If you **did** start with a standalone PE-PostgreSQL node, it continues to function in that capacity and you can retire or repurpose the old PuppetDB node.

**Related information**  


[Generate a token using puppet-access](rbac_token_auth_intro.md#)

## Upgrading PostgreSQL

If you use the default PE-PostgreSQL database installed alongside PuppetDB, you don't have to take special steps to upgrade PostgreSQL. However, if you have a standalone PE-PostgreSQL instance, or if you use a PostgreSQL instance not managed by PE, you must take extra steps to upgrade PostgreSQL.

You must upgrade a standalone PE-PostgreSQL instance each time you upgrade PE. To upgrade a standalone PE-PostgreSQL instance, simply run the installer on the PE-PostgreSQL node first, then proceed with upgrading the rest of your infrastructure.

You must upgrade a PostgreSQL instance not managed by PE only when there's an upgrade to PostgreSQL in PE, which occurred most recently in 2017.3. To upgrade an unmanaged PostgreSQL instance, use one of these methods:

-   Back up databases, wipe your old PostgreSQL installation, install the latest version of PostgreSQL, and restore the databases. 

-   Back up databases, set up a new node with the latest version of PostgreSQL, restore databases to the new node, and reconfigure PE to point to the new `database_host`.

-   Run `pg_upgrade` to get from the older PostgreSQL version to the latest version.


**Note:**  If you're upgrading a split installation of a PE version earlier than 2016.4.3 with an external PostgreSQL instance, you must upgrade with the `--force` flag, for example:

```
/opt/puppetlabs/puppet/bin/puppet infrastructure configure --detailed-exitcodes --modulepath=/opt/puppetlabs/server/data/enterprise/modules --no-noop --upgrade-from=<PREVIOUS PE VERSION> --force
```

Upgrading this configuration without the force flag causes the upgrade to hang while searching for the external database.

## Text mode installer options

When you run the installer in text mode, you can use the `-c` option to specify the full path to an existing `pe.conf` file. You can pair these additional options with the `-c` option.

|Option|Definition|
|------|----------|
|`-D`|Display debugging information|
|`-q`|Run in quiet mode. The installation process isn't displayed. If errors occur during the installation, the command quits with an error message.|
|`-y`|Run automatically using the `pe.conf` file at `/etc/puppetlabs/enterprise/conf.d/`. If the file is not present or is invalid, installation or upgrade fails.|
|`-V`|Display verbose debugging information.|
|`-h`|Display help information.|
|`force`|For upgrades only, bypass PostgreSQL migration validation. This option must appear last, after the end-of-options signifier \(`--`\), for example `sudo ./puppet-enterprise-installer -c pe.conf -- --force`|

## Checking for updates

To see the version of PE you're currently using, run `puppet --version` on the command line. Check the PE download site to find information about the latest maintenance release.

**Note:** By default, the master checks for updates whenever the `pe-puppetserver` service restarts. As part of the check, it passes some basic, anonymous information to Puppet servers. You can optionally disable update checking.

