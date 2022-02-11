---
author: Logan Mantyla <logan.mantyla@puppet.com\>
---

# Backup and restore split installations and upgrades

Backup and restore your Puppet Enterprise \(PE\) using this method if you are on a split installation or if you are migrating to a new version of PE.

## Back up your database and Puppet Enterprise files

To properly back up your PE installation, you'll need to back up certain files and databases.

### About this task

### Procedure

1.  Back up these files:

    -   `/etc/puppetlabs/`---if you back up this directory to `/opt/puppetlabs/server/data/postgres/backups`, you can later access your original installation answer file or `pe.conf` file when you restore. If you're restoring from a version earlier than 2016.2, you need to convert your answer file to the `pe.conf`file format.

    -   The modulepath---if you've configured it to be outside the PE default of `modulepath = /etc/puppetlabs/code/environments/production/modules:/etc/puppetlabs/code/modules:/opt/puppetlabs/puppet/modules` in `puppet.conf`

    -   Ensure any SSH keys used for Code Manager are backed up. This key is stored in `/etc/puppetlabs/puppetserver/conf.d/code-manager.conf` in the `private-key` setting. If the file does not exist, Code Manager is not enabled, and this step is not needed.

2.  Back up your PE PostgreSQL database.

    **Note:** Run all back up and restore commands as the pe-postgres user, which must have write access to the backup location to create the backup, and read access to it to restore it.

    On a split install \(master, console, PuppetDB/ PostgreSQL each on a separate node\), they will be located across the various servers assigned to these PE components.

    -   `/etc/puppetlabs/`: different versions of this directory can be found on the server assigned to the Puppet master component, the server assigned to the console component, and the server assigned to the PuppetDB/ PostgreSQL component. You should back up each version.
    -   `/opt/puppetlabs/server/data/console-services/certs/`: located on the server assigned to the console component.
    -   `/opt/puppetlabs/server/data/postgresql/9.6/data/certs/`: located on the server assigned the PuppetDB
    -   The pe-activity, pe-classifier, pe-orchestrator, pe-puppetdb, and pe-rbac databases: located on the server assigned to the PuppetDB/ PostgreSQL component.

        To back up each database individually, run the following commands:

        ```
        sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_dump -Fc pe-activity -f /tmp/pe-activity_`date +%m_%d_%y_%H_%M`.bin
        sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_dump -Fc pe-rbac -f /tmp/pe-rbac_`date +%m_%d_%y_%H_%M`.bin
        sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_dump -Fc pe-classifier -f /tmp/pe-classifier_`date +%m_%d_%y_%H_%M`.bin
        sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_dump -Fc pe-puppetdb -f /tmp/pe-puppetdb_`date +%m_%d_%y_%H_%M`.bin
        sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_dump -Fc pe-orchestrator -f /tmp/pe-orchestrator_`date +%m_%d_%y_%H_%M`.bin
        ```

        **Tip:** If you are not in a directory that the `pe-postgres` user has permissions to access, you'll receive a 'permission denied' error, even though the `pg_dump` command will succeed.


## Restore your database and Puppet Enterprise files

Restore your database and Puppet Enterprise files.

### About this task

### Procedure

1.  Using the standard install process \(run the `puppet-enterprise-installer` script\), reinstall the same version of Puppet Enterprise that was installed for the files you backed up.

    -   If you're restoring from a version earlier than 2016.2, you need to covert your answer file to the pe.conf file format, and use that during the installation process.

    -   If you need to review the PE installation process, check out Installing Puppet Enterprise.

2.  Stop all PE services, except `pe-postgresql`.

    1.  On the Puppet master, run:

        ```
        puppet resource service puppet ensure=stopped
        puppet resource service pe-puppetserver ensure=stopped
        puppet resource service pe-activemq ensure=stopped
        puppet resource service mcollective ensure=stopped
        puppet resource service pe-orchestration-services ensure=stopped
        puppet resource service pe-nginx ensure=stopped
        
        ```

    2.  Run **both commands** on the node assigned the PuppetDB/PostgreSQL component:

        ```
        puppet resource service pe-puppetdb ensure=stopped
        puppet resource service puppet ensure=stopped			
        ```

    3.  Run **both commands** on the node assigned the console component:

        ```
        puppet resource service pe-console-services ensure=stopped
        puppet resource service puppet ensure=stopped
        ```

3.  Restore your PE PostgreSQL databases. Depending on the size of these databases, restoration might take a considerable amount of time to complete.

    To restore an individual database backup, run the following command: `sudo -u pe-postgres /opt/puppetlabs/server/apps/postgresql/bin/pg_restore -Cc -d template1 <BACKUP_FILE>.bin` This command connects to the `template1` database and then drops the database indicated in `<BACKUP_FILE>.bin`. The command then re-creates that database and connects to the newly created database to perform the restoration.

    If you omit the `-d` parameter, `pg_restore` will output the SQL commands needed to restore a database, but it will not perform the actual restoration.

4.  Stop the `pe-postgresql` service:

    ```
    puppet resource service pe-postgresql ensure=stopped
    ```

5.  From your `/etc/puppetlabs/` backup, restore these directories and files:

    -   `/etc/puppetlabs/puppet/puppet.conf`
    -   `/etc/puppetlabs/puppet/ssl` \(fully replace with backup, do not leave existing ssl data\)
    -   `/etc/puppetlabs/puppetdb/ssl` \(fully replace with backup, do not leave existing ssl data\)
    -   `/opt/puppetlabs/server/data/postgresql/9.6/data/certs/`
    -   `/opt/puppetlabs/server/data/console-services/certs/`
    -   The modulepath—if you've configured it to be something other than the PE default.
    These files and databases should be replaced on the various servers assigned to these PE components.

    -   `/etc/puppetlabs/`: as noted earlier, there is a different version of this directory for the Puppet master component, the console component, and the database support component \(i.e., PuppetDB and PostgreSQL\). You should replace each version.
    -   `/etc/puppetlabs/puppetdb/ssl`: located on the server assigned to the PuppetDB component.
    -   `/opt/puppetlabs/server/data/console-services/certs/`: located on the server assigned to the console component.
    -   `/opt/puppetlabs/server/data/postgresql/9.6/data/certs/`: located on the server assigned the PuppetDB/ PostgreSQL component.
    -   The pe-classifier, pe-rbac, and pe-activity databases: located on the server assigned to the database support component.
    -   The PuppetDB database: located on the server assigned to the database support component.
    -   The modulepath: located on the server assigned to assigned to the Puppet master component.
    If you backed up any Simple RPC agents, you will need to restore these on the same server assigned to the Puppet master component.

6.  Restore modules, manifests, Hiera data, and, if necessary, Code Manager SSH keys. These are typically located in the `/etc/puppetlabs/` directory, but you may have configured them in another location.

7.  Remove the cached catalog on the Puppet master: `rm -f /opt/puppetlabs/puppet/cache/client_data/catalog/<CERTNAME>.json`

8.  Run the following commands:

    ```
    chown pe-puppet:pe-puppet /etc/puppetlabs/puppet/puppet.conf
    chown -R pe-puppet:pe-puppet /etc/puppetlabs/puppet/ssl/
    chown -R pe-console-services /opt/puppetlabs/server/data/console-services/certs/
    chown -R pe-orchestration-services:pe-orchestration-services /etc/puppetlabs/orchestration-services/ssl/
    chown -R pe-postgres:pe-postgres /opt/puppetlabs/server/data/postgresql/9.6/data/certs/
    chown -R pe-puppetdb:pe-puppetdb /etc/puppetlabs/puppetdb/ssl/
    ```

9.  Restart PE services.

    1.  On the Puppet master, run:

        ```
        puppet resource service pe-puppetserver ensure=running
        puppet resource service pe-orchestration-services ensure=running
        puppet resource service pe-activemq ensure=running
        puppet resource service pe-nginx ensure=running
        ```

    2.  Run the following commands on the node assigned the PuppetDB/PostgreSQL component:

        ```
        puppet resource service pe-postgresql ensure=running
        puppet resource service pe-puppetdb ensure=running
        ```

    3.  Run the following on the node assigned the console component: `puppet resource service pe-console-services ensure=running`
    4.  Run the following on the Puppet master, PuppetDB, and console components: `puppet resource service puppet ensure=running`
10. Fix database access privileges with Puppet. Run the following command: `puppet infrastructure configure`.


## Purge the Puppet Enterprise installation \(optional\)

If you're planning on restoring your databases and PE files to the same server\(s\), you'll want to first fully purge your existing Puppet Enterprise installation.

### About this task

### Procedure

1.  You'll need to run the uninstaller script `puppet-enterprise-uninstaller` with the `-p` and `-d` flags on each server that has been assigned a component. It can be found either at`/opt/puppetlabs/bin/puppet-enterprise-uninstaller` or inside the installer tarball you downloaded when setting up PE initially.

2.  After running the uninstaller, ensure that /opt/puppetlabs/ and /etc/puppetlabs/ are no longer present on the system.

    For more information about using the PE uninstaller, refer to Uninstalling Puppet Enterprise.


