---
layout: default
title: "Puppet Enterprise database maintenance"
canonical: "/pe/latest/maintain_console-db.html"
---

If PE's databases become sluggish or begins taking up too much disk space, there are several maintenance tasks that can improve their performance.

## Databases in Puppet Enterprise

PE uses PostgreSQL as the backend for its databases. 

We recommend that you use PostgreSQL’s native tools to perform database exports and imports.  At a minimum, we recommend nightly backups to a remote system, or as dictated by your company policy.

The PE PostgreSQL database includes the following databases:

Database      | Description
------------- | ---------------------------------------------------------------
pe-activity   | Activity data from the Classifier, including who, what and when
pe-classifier | Classification data, all Node Group information
pe-puppetdb   | PuppetDB’s data, including exported resources, catalogs, facts, and reports
pe-rbac       | RBAC data, including users, permissions, and AD/LDAP info
pe-orchestrator | orchestrator data, including details about job runs (users, nodes, and run results) 
 

## Optimizing your databases

In some cases you may find your databases are slow or need general performance enhancements. The PostgreSQL `vacuum` command can help optimize your databases. 

1. To vacuum a particular database, run the following command:

   ~~~
   su - pe-postgres -s /bin/bash -c "vacuumdb -z --verbose <DATABASE NAME>"
   ~~~

## Ways to backup Puppet Enterprise databases

You should perform regular backups of individual Puppet Enterprise databases.  There are fundamentally two ways to backup your PE databases.  The preferred approach is to backup each database individually for the following reasons:

1.  Individual backups can be saved in a compressed format reducing disk I/O and saving space
2.  Individual backups can be restored independent from the other databases
  - A pg_dumpall backup has to restore all databases at the same time
3.  Individual backups can be scheduled independently

### Individual database backup and restore

The `pg_dump` command dumps an individual databases to a compressed binary file on disk, which you can restore at any time.

>**NOTE:** All back up and restore commands must be run as the pe-postgres user.

1. To backup each PE PostgreSQL database individually, run the following commands:

   ~~~bash
   sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_dump -Fc pe-activity -f /tmp/pe-activity_`date +%m_%d_%y_%H_%M`.bin
   sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_dump -Fc pe-rbac -f /tmp/pe-rbac_`date +%m_%d_%y_%H_%M`.bin
   sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_dump -Fc pe-classifier -f /tmp/pe-classifier_`date +%m_%d_%y_%H_%M`.bin
   sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_dump -Fc pe-puppetdb -f /tmp/pe-puppetdb_`date +%m_%d_%y_%H_%M`.bin
   sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_dump -Fc pe-orchestrator -f /tmp/pe-orchestrator_`date +%m_%d_%y_%H_%M`.bin
   ~~~

   This command creates a binary dump of the database.  The `pe-postgres` user needs write access to the output destination.

2. To restore an individual PE PostgreSQL database backup, run the following command:

   ~~~bash
   sudo -u pe-postgres /opt/puppetlabs/server/apps/postgresql/bin/pg_restore -Cc -d template1 <BACKUP_FILE>.bin
   ~~~

   This command will connect to the `template1` database and then drop the database indicated in <BACKUP_FILE>.bin before re-creating that database and connecting to the newly created database to perform the restoration.

   If you omit the `-d` parameter, `pg_restore` will output the SQL commands needed to restore a database to the console, but **it will not perform the actual restoration**.

3. After restoring that database, you fix access privileges for that database with Puppet. Run the following command:

   ~~~
   puppet enterprise configure
   ~~~

### Complete database backup and restore

The `pg_dumpall` command dumps the complete set of PE databases to a SQL file on disk. After you've created a back up of your PE databases, you can restore them at any time.

Before beginning, review the following information:

- All back up and restore commands must be run as the pe-postgres user.

- It isn’t necessary to stop any PE services to perform these steps.

1. To back up the entire PE PostgreSQL database, run the following command:

   ~~~sql
   sudo -u pe-postgres /opt/puppetlabs/server/apps/postgresql/bin/pg_dumpall -c -f <BACKUP_FILE>.sql
   ~~~

   >**Note**: The name and location of your <BACKUP_FILE>.sql can be changed as needed.

2. To begin the restore, stop all PE services except for `pe-postgresql`. Run the following commands:

   ~~~
   puppet resource service puppet ensure=stopped
   puppet resource service pe-puppetserver ensure=stopped
   puppet resource service pe-puppetdb ensure=stopped
   puppet resource service pe-console-services ensure=stopped
   puppet resource service pe-orchestration-services ensure=stopped
   puppet resource service pe-nginx ensure=stopped
   ~~~

3. When restoring the ‘pe-postgres’ user needs read access to the SQL file created when the database was backed up. Run the following command to perform the restore:

   ~~~sql
   sudo -u pe-postgres /opt/puppetlabs/server/apps/postgresql/bin/psql < <BACKUP_FILE>.sql
   ~~~

   This command will import the complete PE PostgreSQL database backup you created in the procedure above.

4. Start all PE services. Run the following commands:

   ~~~
   puppet resource service puppet ensure=running
   puppet resource service pe-puppetserver ensure=running
   puppet resource service pe-puppetdb ensure=running
   puppet resource service pe-console-services ensure=running
   puppet resource service pe-orchestration-services ensure=running
   puppet resource service pe-nginx ensure=running
   ~~~

## Changing the PuppetDB user/password

The console uses a database user account to access its PostgreSQL database. If this user's password is compromised, or if it needs to be changed periodically, do the following:

1. Stop the `pe-puppetdb` service with `puppet resource service pe-puppetdb ensure=stopped`.
2. On the database server (which may or may not be the same as PuppetDB, depending on your deployment's architecture) use the PostgreSQL administration tool of your choice to change the user's password. With the standard `psql` client, you can do this with `ALTER USER console PASSWORD '<new password>';`.
3. Edit `/etc/puppetlabs/puppetdb/conf.d/database.ini` on the PuppetDB server and change the `password:` line under __common__ (or under __production,__ depending on your configuration) to contain the new password.
4. Start the `pe-puppetdb` service on the console server with with `puppet resource service pe-puppetdb ensure=running`.

## Changing PuppetDB’s parameters

PuppetDB parameters are set in the `jetty.ini` file, which is contained in the pe-puppetdb module. Jetty.ini is managed by PE, so if you change any PuppetDB parameters directly in the file, those changes will be overwritten on the next Puppet run.

Instead, you should use the console to make changes to the parameters of the `pe-puppetdb` class. For example, the [PuppetDB performance dashboard]({{puppetdb}}/maintain_and_tune.html) requires the `listen_address` parameter to be set to “0.0.0.0”. So, in the console, you would edit the `pe_puppetdb` class so that the value of the `listen_address` parameter is set to “0.0.0.0”.

> **Warning**: This procedure will enable insecure access to the PuppetDB instance on your server.

If you are unfamiliar with editing class parameters in the console, refer to [editing class parameters on nodes](/pe/latest/console_classes_groups_making_changes.html#editing-parameters).


