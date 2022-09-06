# Upgrading Puppet Enterprise

Upgrade your PE installation as new versions become available.

## Upgrade paths

These are the valid upgrade paths for PE.

|If you're on version...|Upgrade to...|Notes|
|-----------------------|-------------|-----|
|2019.1.z|You're up to date!||
|2019.0.z|2019.1| |
|2018.1.1 or later|2019.1|You must have version 2018.1.1 or later in order to complete prerequisites for upgrade to 2019.1. For details, see [Upgrade cautions](upgrading_pe.md#).|
|2018.1.0|2018.1.z|
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

These are the major updates to recent PE versions that you should be aware of when upgrading.

**Important:** Always back up your installation before performing any upgrade.

### TLSv1 and v1.1 disabled in PE 2019.1

As of PE 2019.1, TLSv1 and TLSv1.1 is now disabled by default to comply with security regulations. You must [enable TLSv1](enable_tlsv1.md) to upgrade agents on these platforms:

-   AIX

-   CentOS 5

-   RHEL 5

-   SLES 11

-   Solaris 10

-   Windows Server 2008r2


### Migration of PuppetDB `resource_events` table in PE 2019.1

Upgrading from PE versions prior to 2019.1 requires PuppetDB to perform a migration of the `resource_events` table. This migration can take a couple of hours to complete for installations with thousands of agents. The migration also produces a lot of disk writes, which can increase the performance overhead of VM snapshots. If you have installations large enough to be using an external database node, consider a database backup as a rollback strategy instead of a VM snapshot.

**Truncating the `resource_events` table to decrease migration time**

Truncating the `resource_events` can significantly reduce migration time and lessen your downtime, especially if your table is larger than a few gigabytes \(GB\). If you truncate the table, the Events page in the PE console will be temporarily blank after the upgrade. The data will be repopulated with the restoration of regular Puppet runs.

To determine the size of your `resource_events` table, run the following command on the node where PostgreSQL is running \(the master node in a monolithic installation, the PuppetDB node in a split installation, or the standalone PE-PostgreSQL node\):

```
su pe-postgres --shell /bin/bash --command "/opt/puppetlabs/server/bin/psql --dbname pe-puppetdb --command \"SELECT relname, pg_size_pretty(pg_relation_size(oid)) AS size FROM pg_class WHERE relname='resource_events';\""
```

**Truncate the `resource_events` table on a monolithic installation**

To truncate your `resource_events` table on a monolithic installation, run the following command on your master:

```
su - pe-postgres --shell /bin/bash --command "/opt/puppetlabs/server/bin/psql --dbname pe-puppetdb --command 'TRUNCATE resource_events'"
```

**Truncate the `resource_events` table on a high availability installation**

To truncate your `resource_events` table on a high availability installation:

1.  Stop PuppetDB on your master and replica:

    ```
    sudo puppet resource service puppetdb ensure=stopped
    ```

2.  Run the truncation command on the node where PostgreSQL is running \(the master in a monolithic installation, or the standalone PE-PostgreSQL node\):

    ```
    su - pe-postgres --shell /bin/bash --command "/opt/puppetlabs/server/bin/psql --dbname pe-puppetdb --command 'TRUNCATE resource_events'"
    ```

3.  Restart PuppetDB on your master and replica:

    ```
    sudo puppet resource service puppetdb ensure=running
    ```


**Truncate the `resource_events` table on a split installation**

To truncate your `resource_events` table on a split installation, run the following command on the node where PostgreSQL is running \(generally the PuppetDB node in a split installation, or the standalone PE-PostgreSQL node\):

```
su - pe-postgres --shell /bin/bash --command "/opt/puppetlabs/server/bin/psql --dbname pe-puppetdb --command 'TRUNCATE resource_events'"
```

**Note:** If you are running a PostgreSQL instance not managed by PE, you might need to alter the truncation command to match your environment.

### Certificate architecture and handling in PE 2019.0

PE 2019.0 and later, courtesy of Puppet Server, uses an intermediate certificate authority architecture by default. When you upgrade to PE 2019.0 or later, you can optionally [regenerate certificates](https://puppet.com/docs/pe/2019.0/regenerate_certificates.html#regenerate-certificates-in-pe-monolithic-installs) to adopt the intermediate certificate architecture.

To adopt the new CA architecture, both your master and agents must be upgraded, and you must regenerate certificates. You can use pre-6.x agents with a Puppet 6.x or PE 2019.0 or later master, but this combination doesn't take advantage of the new intermediate certificate authority architecture. If you don't upgrade *all *of your nodes to 6.x, don't regenerate certificates, because pre-6.x agents won't work with the new CA architecture. 

### MCollective removal in PE 2019.0

If you're upgrading from a 2018.1 installation with MCollective enabled, you must take additional steps to ensure a successful upgrade.

**Before upgrade**

-   [Remove MCollective](upgrading_pe.md#) from nodes in your infrastructure. If any nodes are configured with MCollective or ActiveMQ profiles when you attempt to upgrade, the installer halts and prompts you to remove the profiles. For example, remove PE MCollective node group and any of the deprecated parameters:

-   mcollective\_middleware\_hosts
-   mcollective
-   mcollective\_middleware\_port
-   mcollective\_middleware\_user
-   mcollective\_middleware\_password
**Tip:** If your PuppetDB includes outdated catalogs for nodes that aren't currently being managed, the installer might report that MCollective is active on those nodes. You can deactivate the nodes with `puppet node deactivate`or use Puppet to update the records.


**After upgrade**

-   Manually remove these node groups:

-   **PE MCollective**

-   **PE ActiveMQ Broker**

-   Any custom node group you created for ActiveMQ hubs

-   If you customized classification with references to MCollective or ActiveMQ profiles, remove the profiles from your classification. In this version of PE, nodes that include MCollective or ActiveMQ profiles trigger a warning during agent runs. Future versions of PE that remove the profiles completely can trigger failures in catalog compilation if you leave the profiles in place.


### Removing MCollective

Remove MCollective and its related files from the nodes in your infrastructure. You must have PE version 2018.1.1 or later to complete this task.

#### About this task

**Note:** This procedure does not remove your MCollective or ActiveMQ log files.

#### Procedure

1.  In the console, click **Classification**, and select the node group **PE Infrastructure**.

2.  On the **Configuration** tab, find the **puppet\_enterprise** class. Select the **mcollective** parameter and edit its value to absent.

    |Class|Parameter|Value|
    |-----|---------|-----|
    |`puppet_enterprise`|`mcollective`|`absent`|

3.  Click **Add parameter** and commit the change.

4.  Set up a job and run Puppet on the **PE Infrastructure** node group to enforce your changes.


#### Results

The server components of MCollective, including pe-activemq and the peadmin user, are removed from the master and the MCollective service on agents is stopped. You must complete the upgrade to 2019.0 or later to completely remove MCollective from agents.

## Test modules before upgrade

To ensure that your modules work with the newest version of PE, update and test them with Puppet Development Kit \(PDK\) before upgrading.

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

To upgrade, run the text-based PE installer on your master, and then upgrade any additional components. To upgrade with high availability enabled, you must also run the upgrade script on your replica.

### Before you begin

Back up your PE installation.

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

4.  If prompted, change the console administrator password by following the password reset link or running `puppet infra console_password --password=<MY_PASSWORD>`

    For security purposes, you're prompted to change the password if you didn't do so when you originally installed. If you don't change the password, the console administrator account is revoked.

5.  If you have compilers, upgrade them.

    SSH into each compiler and run:

    ```
    /opt/puppetlabs/puppet/bin/curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<PUPPET MASTER FQDN>:8140/packages/current/upgrade.bash | sudo bash
    ```

6.  Upgrade these additional PE infrastructure components.

    -   Agents

    -   Razor

    -   PE client tools — Install the appropriate version of client tools that matches the PE version you upgraded to.

7.  In high availability installations, upgrade your replica.

    The replica is temporarily unavailable to serve as backup during this step — time re-creating a replica to minimize risk.

    1.  On your replica, run the upgrade script:

        ```
        curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<MASTER_HOST>:8140/packages/current/upgrade.bash | bash
        ```

    2.  Verify that master and replica services are operational:

        ```
        /opt/puppetlabs/bin/puppet-infra status
        ```

    3.  If your replica reports errors, reinitialize the replica:

        ```
        /opt/puppetlabs/bin/puppet-infra reinitialize replica -y
        ```


**Related information**  


[Backing up and restoring Puppet Enterprise](backing_up_and_restoring_pe.md#)

[Back up your PE infrastructure](backing_up_and_restoring_pe.md#)

[Forget a replica](configure_high_availability.md#)

[Provision a replica](configure_high_availability.md#)

[Enable a replica](configure_high_availability.md#)

## Upgrade a split installation

To upgrade a split or large environment installation, run the text-based installer on each infrastructure node in your environment, and then upgrade any additional components.

### Before you begin

Back up your PE installation.

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

4.  If prompted, change the console administrator password by following the password reset link or running `puppet infra console_password --password=<MY_PASSWORD>`

    For security purposes, you're prompted to change the password if you didn't do so when you originally installed. If you don't change the password, the console administrator account is revoked.


### Run Puppet on infrastructure nodes

To complete a split upgrade, run Puppet on all infrastructure nodes in the order that they were upgraded.

#### About this task

#### Procedure

1.  Run Puppet on the master node.

2.  Run Puppet on the PuppetDB node.

3.  Run Puppet on the console node.


### Upgrade remaining infrastructure nodes

After the main components of your infrastructure are upgraded, you must upgrade any additional infrastructure nodes, such as compilers, hubs, and spokes.

#### Procedure

1.  If you have compilers, upgrade them.

    SSH into each compiler and run:

    ```
    /opt/puppetlabs/puppet/bin/curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<PUPPET MASTER FQDN>:8140/packages/current/upgrade.bash | sudo bash
    ```

2.  Upgrade these additional PE infrastructure components.

    -   Agents

    -   Razor

    -   PE client tools — Install the appropriate version of client tools that matches the PE version you upgraded to.


## Migrate from a split to a monolithic installation

Split installations, where the master, console, and PuppetDB are installed on separate nodes, are deprecated. Migrate from an existing split installation to a monolithic installation—with or without compilers—and a standalone PE-PostgreSQL node.

### Before you begin

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


## Checking for updates

To see the version of PE you're currently using, run `puppet --version` on the command line. Check the PE download site to find information about the latest maintenance release.

**Note:** By default, the master checks for updates whenever the `pe-puppetserver` service restarts. As part of the check, it passes some basic, anonymous information to Puppet servers. You can optionally disable update checking.

