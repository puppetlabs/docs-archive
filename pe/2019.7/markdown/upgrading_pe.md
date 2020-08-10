# Upgrading Puppet Enterprise

Upgrade your PE installation as new versions become available.

## Upgrade paths

These are the valid upgrade paths for PE.

|If you're on version...|Upgrade to...|Notes|
|-----------------------|-------------|-----|
|2019.7 \(latest\)|You're up to date!| |
|2019.y|latest| |
|2018.1.2 or later2018.1.3 or later with disaster recovery

|latest|You must have version 2018.1.2 or later in order to complete prerequisites for upgrade to the latest version.With disaster recovery enabled, you must have version 2018.1.3 in order to upgrade to the latest version. Alternatively, you can forget and then recreate your replica after upgrade.

|
|2018.1.0 or 2018.1.1|2018.1.z|
|2017.3.z|2018.1| |
|2017.2.z|2018.1| |
|2017.1.z|2018.1| |
|2016.5.z|2018.1| |
|2016.4.10 or later|2018.1| |
|2016.4.9 or earlier|2016.4.z, then 2018.1|To upgrade to 2018.1 from 2015.2.z through 2016.4.9, you must first [upgrade to the latest 2016.4.z](https://docs.puppet.com/pe/2016.4/upgrade_mono.html).|
|2016.2.z|
|2016.1.z|
|2015.3.z|
|2015.2.z|
|3.8.x|2016.4.z, then 2018.1|To upgrade from 3.8.x, you must first [migrate to the latest 2016.4.z](https://docs.puppet.com/pe/2016.4/migrate_pe_catalog_preview.html). This upgrade requires a different process than upgrades from other versions.|

## Upgrade cautions

These are the major updates to PE since the last long-term support release, 2018.1. Review these recommendations and plan accordingly before upgrading to this version.

**Important:** Always back up your installation before performing any upgrade.

### PuppetDB migrations in PE 2019.1, 2019.3, and 2019.7

Upgrades to PE 2019.1, 2019.3, and 2019.7 involve database migrations that can slow upgrade significantly. Deleting PuppetDB reports and truncating the resource events table before you upgrade can reduce migration time and lessen your downtime.

If you're upgrading from 2019.7 or later, or PE LTS version 2018.1.15 or later, you can run a packaged delete reports command before [upgrade](upgrading_pe.md#). For other versions that don't include the delete reports command, you can manually delete reports by following these steps.

1.  Stop the PuppetDB service: `service pe-puppetdb stop`
2.  On your PE-PostgreSQL server, create a file named `/tmp/delete-reports.sql` and set it to be owned by the `pe-postgres` user \(`chown pe-postgres:pe-postgres /tmp/delete-reports.sql`\).
3.  Add contents to the `.sql` file according to your PE version.

    PE versions earlier than 2019.3

    ```
    BEGIN TRANSACTION;
                                
                                ALTER TABLE certnames DROP CONSTRAINT IF EXISTS certnames_reports_id_fkey;
                                UPDATE certnames SET latest_report_id = NULL;
                                TRUNCATE TABLE reports CASCADE;
                                
                                ALTER TABLE certnames
                                ADD CONSTRAINT certnames_reports_id_fkey
                                FOREIGN KEY (latest_report_id) REFERENCES reports(id) ON DELETE SET NULL;
                                
                                COMMIT TRANSACTION;
    ```

    PE 2019.3 through 2019.7

    ```
    BEGIN TRANSACTION;
                            
                            ALTER TABLE certnames DROP CONSTRAINT IF EXISTS certnames_reports_id_fkey;
                            UPDATE certnames SET latest_report_id = NULL;
                            
                            DO $$ DECLARE
                            r RECORD;
                            BEGIN
                            FOR r IN (SELECT tablename FROM pg_tables WHERE tablename LIKE 'resource_events_%') LOOP
                            EXECUTE 'DROP TABLE ' || quote_ident(r.tablename);
                            END LOOP;
                            END $$;
                            
                            TRUNCATE TABLE reports CASCADE;
                            
                            ALTER TABLE certnames
                            ADD CONSTRAINT certnames_reports_id_fkey
                            FOREIGN KEY (latest_report_id) REFERENCES reports(id) ON DELETE SET NULL;
                            
                            COMMIT TRANSACTION;
    ```

4.  Run the command: `su - pe-postgres -s /bin/bash -c "/opt/puppetlabs/server/bin/psql -d pe-puppetdb -f /tmp/delete-reports.sql"`

### Java 11 upgrade in PE 2019.3

PE 2019.3 includes an upgrade from Java version 8 to version 11. If you've customized PE Java services, or use plug-ins that include Java code, test PE 2019.3 and later thoroughly in a non-production environment before upgrading.

### Orchestrator memory use increase in PE 2019.2

Puppet orchestrator uses more memory in version 2019.2 than in previous versions due to the addition of a Java virtual machine \(JVM\), which enables new features and functionalities such as plans. If your memory use is near capacity when running PE 2019.1 or older versions, allocate additional memory before upgrading to PE 2019.2.

Additionally, take care when writing plans, as they can require more memory than is allocated to the orchestrator. To work around this issue, rewrite the plan or increase the memory allocated to the orchestrator.

### PostgreSQL 11 upgrade in PE 2019.2

PE 2019.2.0 includes an upgrade from `pe-postgresql` version 9.6 to version 11. As with any major version bump of PostgreSQL, the datastore must be migrated to a format compatible with the new version of PostgreSQL. The PE installer performs this migration automatically using the PostgreSQL `pg_upgrade` utility. Because both the 9.6 datastore and the new 11 datastore remain present on disk, the partition used for PostgreSQL must have enough space for the migrated datastore \(calculated with a margin as 110% of the current 9.6 datastore\). The installer issues a warning and cancels the upgrade if there is insufficient space.

The datastore migration also increases the amount of time required to complete the upgrade. The time required varies depending on your installation's size and hardware setup, but broadly, expect between two and four minutes of additional time per 10GB of datastore size.

The `pe-modules` `pe_postgresql_info` fact provides information about the size of your PostgreSQL installation as well as the size and number of available bytes for the partition. To review this fact, run `facter -p pe_postgresql_info` on the PE node that runs the `pe-postgresql` service \(either the master node or the standalone PostgreSQL node in extra-large installations\).

**Note:** If you use an external PostgreSQL instance not managed by PE, you must separately upgrade the instance to PostgreSQL 11. For more information, see [Upgrading PostgreSQL](upgrading_pe.md#).

After upgrading, you can optionally remove packages and directories associated with older PostgreSQL versions with the command `puppet infrastructure run remove_old_postgresql_versions`. If applicable, the installer prompts you to complete this cleanup.

### TLSv1 and v1.1 disabled in PE 2019.1

As of PE 2019.1, TLSv1 and TLSv1.1 is now disabled by default to comply with security regulations. You must [enable TLSv1](enable_tlsv1.md) to upgrade agents on these platforms:

-   AIX

-   CentOS 5

-   RHEL 5

-   SLES 11

-   Solaris 10

-   Windows Server 2008 R2


### Certificate architecture and handling in PE 2019.0

PE 2019.0 and later, courtesy of Puppet Server, uses an intermediate certificate authority architecture by default. When you update to PE 2019.0 or later, you have several options for whether and when to adopt the new intermediate certificate architecture:

-   **To *upgrade* to 2019.0 or later and keep your existing CA**, upgrade infrastructure nodes and agents as normal. You can continue to use pre-6.x agents with a Puppet 6.x or PE 2019.0 or later master as long as you don't regenerate certificates.
-   **To *migrate* to 2019.0 or later and keep your existing CA**, install the new version and copy `/etc/puppetlabs/puppet/ssl` from your old master. You can continue to use pre-6.x agents with a Puppet 6.x or PE 2019.0 or later master as long as you don't regenerate certificates.
-   **To adopt the new CA architecture**, upgrade both your master and agents, and then [regenerate certificates](https://puppet.com/docs/pe/2019.0/regenerate_certificates.html#regenerate-certificates-in-pe-monolithic-installs). If you don't upgrade all of your nodes to 6.x, don't regenerate certificates, because pre-6.x agents won't work with the new CA architecture.

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


#### Removing MCollective

Remove MCollective and its related files from the nodes in your infrastructure. You must have PE version 2018.1.1 or later to complete this task.

**Note:** This procedure does not remove your MCollective or ActiveMQ log files.

1.  In the console, click **Classification**, and select the node group **PE Infrastructure**.

2.  On the **Configuration** tab, find the **puppet\_enterprise** class. Select the **mcollective** parameter and edit its value to absent.

    |Class|Parameter|Value|
    |-----|---------|-----|
    |`puppet_enterprise`|`mcollective`|`absent`|

3.  Click **Add parameter** and commit the change.

4.  Set up a job and run Puppet on the **PE Infrastructure** node group to enforce your changes.


The server components of MCollective, including pe-activemq and the peadmin user, are removed from the master and the MCollective service on agents is stopped. You must complete the upgrade to 2019.0 or later to completely remove MCollective from agents.

## Test modules before upgrade

To ensure that your modules work with the newest version of PE, update and test them with Puppet Development Kit \(PDK\) before upgrading.

If you are already using PDK, your modules should pass validation and unit tests with your currently installed version of PDK. 

Update PDK with each new release to ensure compatability with new versions of PE.

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


After you've verified that your modules work with the new PE version, you can continue with your upgrade.

## Upgrade a standard installation

To upgrade, run the PE installer on your master, and then upgrade any additional components.

Back up your PE installation.

1.  Speed upgrade by cleaning up PuppetDB reports. On your master, run `/opt/puppetlabs/bin/puppetdb delete-reports`

    If the command fails to execute, you're likely using a version of PuppetDB that doesn't yet include the command. See [Upgrade cautions](upgrading_pe.md#) for manual steps.

2.  [Download](https://puppet.com/try-puppet/puppet-enterprise/download/) the tarball appropriate to your operating system and architecture.

3.  Unpack the installation tarball: `tar -xf <tarball>`

    You need about 1 GB of space to untar the installer.

4.  From the installer directory, run the installer: `sudo ./puppet-enterprise-installer`

    **Note:** To specify a different `pe.conf` file other than the existing file, use the -c flag: `sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>`

    With this flag, your previous `pe.conf` is backed up to `/etc/puppetlabs/enterprise/conf.d/<TIMESTAMP>.conf` and a new `pe.conf` is created at `/etc/puppetlabs/enterprise/conf.d/pe.conf`.

5.  If prompted, change the console administrator password: `puppet infrastructure console_password --password=<MY_PASSWORD>`

    For security purposes, you're prompted to change the password if you didn't do so when you originally installed. If you don't change the password, the console administrator account is revoked.

6.  If you have compilers, on your master logged in as root, run: `puppet infrastructure upgrade compiler <COMPILER_FQDN-1>,<COMPILER_FQDN-2>`

    **Tip:** To upgrade all compilers, run: `puppet infrastructure upgrade compiler --all`

7.  Upgrade these additional PE infrastructure components.

    -   Agents

    -   Razor

    -   PE client tools — Install the appropriate version of client tools that matches the PE version you upgraded to.

8.  In high availability installations, upgrade your replica.

    The replica is temporarily unavailable to serve as backup during this step, so you should time upgrading your replica to minimize risk.

    1.  On your master logged in as root, run: `puppet infrastructure upgrade replica <REPLICA_FQDN>`

    2.  After the replica upgrade successfully completes, verify that master and replica services are operational. On your master, run: `/opt/puppetlabs/bin/puppet-infra status`

    3.  If your replica reports errors, reinitialize the replica. On your replica, run: `/opt/puppetlabs/bin/puppet-infra reinitialize replica -y`

9.  Convert legacy compilers to the new style compiler running the PuppetDB service.

    1.  Open port 5432 from compilers to your master.

    2.  On your master logged in as root, run: `puppet infrastructure run convert_legacy_compiler compiler=<COMPILER_FQDN-1>,<COMPILER_FQDN-2>` or `puppet infrastructure run convert_legacy_compiler all=true`


**Related information**  


[Back up your infrastructure](backing_up_and_restoring_pe.md#)

## Migrate from a split to a standard installation

Split installations, where the master, console, and PuppetDB are installed on separate nodes, are no longer supported as of PE version 2019.2. Before upgrading to 2019.2 or later, migrate from an existing split installation to a standard \(formerly called monolithic\) installation—with or without compilers—and a standalone PE-PostgreSQL node.

You must be running a version of PE on all infrastructure nodes that includes the `puppet infrastructure run` command. To verify that this command is available on your systems, run `puppet infrastructure run --help`.

The `puppet infrastructure run` command leverages built-in Bolt plans to automate certain management tasks. To use this command, you must be able to connect using SSH from your master to any nodes that the command modifies. You can establish an SSH connection using key forwarding, a local key file, or by specifying keys in `.ssh/config` on your master. For more information, see [Bolt OpenSSH configuration options](https://puppet.com/docs/bolt/latest/bolt_configuration_options.html#openssh-configuration-options).

**Important:** The migration command used in this task automates a number of manual steps, including editing `pe.conf`, unpinning and uninstalling packages from affected infrastructure nodes, and running Puppet multiple times. Treat this process as you would any major migration by thoroughly testing it in an environment that's as similar to your production environment as possible.

1.  On your master, verify that `pe.conf` contains correct information for `console_host`, `puppetdb_host`, and `database_host`.

    The migration command uses this information to correctly migrate these nodes.

    **Note:** If your split PE installation includes multiple standalone PuppetDB nodes, the migration command will fail with an error.

2.  Make sure that the master can connect via SSH to your console node, PuppetDB node, and \(if present\) standalone PE-PostgreSQL node.

3.  On your master logged in as root, run `puppet infrastructure run migrate_split_to_mono`

    You can specify this optional parameter:

    -   `tmpdir` — Path to a directory to use for uploading and executing temporary files.

After completion, your master is running the console and PuppetDB services and you can retire or repurpose the old console node. If you **did not** start with a standalone PE-PostgreSQL node, your old PuppetDB node now functions in that capacity. If you **did** start with a standalone PE-PostgreSQL node, it continues to function in that capacity and you can retire or repurpose the old PuppetDB node.

## Upgrading PostgreSQL

If you use the default PE-PostgreSQL database installed alongside PuppetDB, you don't have to take special steps to upgrade PostgreSQL. However, if you have a standalone PE-PostgreSQL instance, or if you use a PostgreSQL instance not managed by PE, you must take extra steps to upgrade PostgreSQL.

You must upgrade a standalone PE-PostgreSQL instance each time you upgrade PE. To upgrade a standalone PE-PostgreSQL instance, simply run the installer on the PE-PostgreSQL node first, then proceed with upgrading the rest of your infrastructure.

You must upgrade a PostgreSQL instance not managed by PE only when there's an upgrade to PostgreSQL in PE. To upgrade an unmanaged PostgreSQL instance, use one of these methods:

-   Back up databases, wipe your old PostgreSQL installation, install the latest version of PostgreSQL, and restore the databases.

-   Back up databases, set up a new node with the latest version of PostgreSQL, restore databases to the new node, and reconfigure PE to point to the new `database_host`.

-   Run `pg_upgrade` to get from the older PostgreSQL version to the latest version.


## Checking for updates

To see the version of PE you're currently using, run `puppet --version` on the command line. Check the PE download site to find information about the latest maintenance release.

**Note:** By default, the master checks for updates whenever the `pe-puppetserver` service restarts. As part of the check, it passes some basic, anonymous information to Puppet servers. You can optionally disable update checking.

