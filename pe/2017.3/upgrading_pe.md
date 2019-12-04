# Upgrading Puppet Enterprise

Upgrade your PE installation as new versions become available.

## Upgrade paths

These are the valid upgrade paths for PE.

**Note:** Beginning with PE 2015.2, Puppet *supports* upgrade from any version, and *tests* upgrade from the last .z release.

|If you're on version...|Upgrade to...|
|-----------------------|-------------|
|2017.3.z|N/A — You're up to date.|
|2017.2.z|2017.3, or wait for the next LTS release.|
|2017.1.z|2017.3, or wait for the next LTS release.|
|2016.5.z|2017.3, or wait for the next LTS release.|
|2016.4.z \(LTS\)|2017.3, or wait for the next LTS release.|
|2016.2.z|[2016.4](https://docs.puppet.com/pe/2016.4/upgrade_mono.html) or 2017.3.|
|2016.1.z|[2016.4](https://docs.puppet.com/pe/2016.4/upgrade_mono.html) or 2017.3.|
|2015.3.z|[2016.4](https://docs.puppet.com/pe/2016.4/upgrade_mono.html) or 2017.3.|
|2015.2.z|[2016.4](https://docs.puppet.com/pe/2016.4/upgrade_mono.html) or 2017.3.|
|3.8.x|[2016.4](https://docs.puppet.com/pe/2016.4/migrate_pe_catalog_preview.html).**Note:** Upgrading from PE 3.8 requires a different process than upgrades from other versions.

|

## Upgrade a monolithic installation

To upgrade a monolithic installation, run the text-based PE installer on your master or master of masters, and then upgrade any additional components.

### About this task

If you encounter errors during upgrade, you can fix them and run the installer again.

### Procedure

1.  [Download](https://puppet.com/download-puppet-enterprise) the tarball appropriate to your operating system and architecture.

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


## Upgrade to PE 2017.3 or later with high availability enabled

Due to a PostgreSQL upgrade in PE 2017.3, you must re-configure high availabity when you upgrade from a PE version earlier than 2017.3 to PE 2017.3 or later.

### About this task

This upgrade requires that you forget and then re-create your replica. The replica is temporarily unavailable to serve as backup during this process, so you should time your upgrade to minimize risk.

### Procedure

1.  Back up your installation.

2.  Upgrade PE.

3.  Forget your existing replica.

4.  Provision a new replica.

5.  Enable the new replica.


## Upgrade a split or large environment installation

To upgrade a split or large environment installation, run the text-based installer on each infrastructure node in your environment, and then upgrade any additional components.

### About this task

**Note:** You must upgrade the components in the order specified.

If you encounter errors during upgrade, you can fix them and run the installer again.

### Upgrade the master

Upgrading the master is the first step in upgrading a split or large environment installation.

#### About this task

#### Procedure

1.  [Download](https://puppet.com/download-puppet-enterprise) the tarball appropriate to your operating system and architecture.

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

