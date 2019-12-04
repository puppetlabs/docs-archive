# Puppet Enterprise database maintenance

You can optimize the Puppet Enterprise \(PE\) databases to improve performance. We recommend that you regularly back up the databases.

## Databases in Puppet Enterprise

PE uses PostgreSQL as the backend for its databases. Use the native tools in PostgreSQL to perform database exports and imports. At a minimum, back up to a remote system nightly, or as specified by your company policy.

The PE PostgreSQL database includes the following databases:

|Database|Description|
|--------|-----------|
|pe-activity|Activity data from the Classifier, including who, what and when|
|pe-classifier|Classification data, all node group information|
|pe-puppetdb|PuppetDB data, including exported resources, catalogs, facts, and reports|
|pe-rbac|Role-based access control data, including users, permissions, and AD/LDAP info|
|pe-orchestrator|Orchestrator data, including user, node, and result details about job runs|

## Optimize a database

If your databases are slow, begin taking up too much disk space, or need general performance enhancements, use the PostgreSQL `vacuum` command to optimize any of the PE databases.

### Procedure

1.  To optimize a database, run: `su - pe-postgres -s /bin/bash -c "vacuumdb -z --verbose <DATABASE NAME>"`


### Results

## Backing up Puppet Enterprise databases

You should regularly back up the databases.

Although you can back up all the databases at one time, it's better to back up each database individually, for the following reasons:

-   Individual backups can be saved in a compressed format, saving space and reducing disk input and output.
-   Individual backups can be restored independently from the other databases. A backup of all of them has to also restore all databases at the same time.
-   Individual backups can be scheduled independently.

### Back up and restore databases individually

Run the `pg_dump` command to back up each database to a compressed binary file on disk, which you can restore by running the `pg_restore` command.

#### About this task

**Note:** Run all back up and restore commands as the pe-postgres user, which must have write access to the backup location to create the backup, and read access to it to restore it.

#### Procedure

1.  To back up each database individually, run the following commands:

    ```
    sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_dump -Fc pe-activity -f /tmp/pe-activity_`date +%m_%d_%y_%H_%M`.bin
    sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_dump -Fc pe-rbac -f /tmp/pe-rbac_`date +%m_%d_%y_%H_%M`.bin
    sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_dump -Fc pe-classifier -f /tmp/pe-classifier_`date +%m_%d_%y_%H_%M`.bin
    sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_dump -Fc pe-puppetdb -f /tmp/pe-puppetdb_`date +%m_%d_%y_%H_%M`.bin
    sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_dump -Fc pe-orchestrator -f /tmp/pe-orchestrator_`date +%m_%d_%y_%H_%M`.bin
    ```

2.  To restore an individual database backup, run the following command: `sudo -u pe-postgres /opt/puppetlabs/server/apps/postgresql/bin/pg_restore -Cc -d template1 <BACKUP_FILE>.bin`

    This command connects to the `template1` database and then drops the database indicated in <BACKUP\_FILE\>.bin. The command then re-creates that database and connects to the newly created database to perform the restoration.

    If you omit the `-d` parameter, `pg_restore` will output the SQL commands needed to restore a database, but it will not perform the actual restoration.

3.  After restoring the database, fix access privileges for that database with Puppet. Run the following command: `puppet infrastructure configure`


### Back up and restore all databases at one time

Run the `pg_dumpall` command to back up the complete set of PE databases to a SQL file on disk. After you've created a backup of your PE databases, you can restore them with the `psql` command.

#### About this task

**Note:** Run all back up and restore commands as the pe-postgres user, which must have write access to the backup location to create the backup, and read access to it to restore it.

-   It isn’t necessary to stop any PE services to perform these steps.


#### Procedure

1.  To back up all of the PE databases together, run the following command: `sudo -u pe-postgres /opt/puppetlabs/server/apps/postgresql/bin/pg_dumpall -c -f <BACKUP_FILE>.sql`

    For `<BACKUP_FILE>`, specify the name and location of the backup file to the location where you want to store the backup.

2.  When you need to restore, stop all PE services except for `pe-postgresql` by running the following commands:

    ```
    puppet resource service puppet ensure=stopped
    puppet resource service pe-puppetserver ensure=stopped
    puppet resource service pe-puppetdb ensure=stopped
    puppet resource service pe-console-services ensure=stopped
    puppet resource service pe-orchestration-services ensure=stopped
    puppet resource service pe-nginx ensure=stopped
    
    ```

3.  Restore all the databases by running: `sudo -u pe-postgres /opt/puppetlabs/server/apps/postgresql/bin/psql < <BACKUP_FILE>.sql`

    This command will import the complete PE database backup.

4.  After the restoration is complete, start all PE services by running the following commands:

    ```
    puppet resource service puppet ensure=running
    puppet resource service pe-puppetserver ensure=running
    puppet resource service pe-puppetdb ensure=running
    puppet resource service pe-console-services ensure=running
    puppet resource service pe-orchestration-services ensure=running
    puppet resource service pe-nginx ensure=running
    ```


