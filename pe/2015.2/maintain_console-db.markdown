---
layout: default
title: "PE 2015.2 » Maintenance » Database"
subtitle: "Maintaining the PE Databases"
canonical: "/pe/latest/maintain_console-db.html"
---


If PE's console becomes sluggish or begins taking up too much disk space, there are several maintenance tasks that can improve its performance.

Optimizing Your Databases
-----

In some cases you may find your databases are slow or need general performance enhancements. The PostgreSQL `vacuum` command can help optimize your databases. 

To vacuum a particular database, use the following command:

~~~
su - pe-postgres -s /bin/bash -c "vacuumdb -z --verbose <DATABASE NAME>"
~~~

The available databases are pe-activity, pe-classifier, pe-puppetdb, and pe-rbac.

Database Backups
----------------

Puppet Enterprise uses PostgreSQL as a database backend. We recommend that you use PostgreSQL’s native tools to perform database exports and imports.  At a minimum, we recommend nightly backups to a remote system, or as dictated by your company policy.

The PE PostgreSQL database (in 2015.2) includes the following databases:

Database      | Description
------------- | ---------------------------------------------------------------
pe-activity   | Activity data from the Classifier, including who, what and when
pe-classifier | Classification data, all Node Group information
pe-puppetdb   | PuppetDB’s data, including exported resources, catalogs, facts, and reports
pe-rbac       | RBAC data, including users, permissions, and AD/LDAP info

## Backup and Restore Procedures for the PE Databases

### Complete Database Backup

**NOTE:** All back up and restore commands must be run as the pe-postgres user.

To back up the entire PE PostgreSQL database, run the following command:

~~~sql
 sudo -u pe-postgres /opt/puppetlabs/server/apps/postgresql/bin/pg_dumpall -c -f <BACKUP_FILE>.sql
~~~

>**Note**: The name and location of your <\BACKUP_FILE>.sql can be changed as needed.

This command, using `pg_dumpall`, dumps the databases to an SQL file on disk.  The `pe-postgres` user needs write access to the output destination.

**Note**: It isn’t necessary to stop any PE services to perform these steps.

### Restore Complete Database Backup

To restore the entire PE PostgreSQL database backup:

1. Since all databases will be destroyed and re-created in this procedure, you must first stop all PE services except for `pe-postgres`. Run the following commands:

   ~~~
   puppet resource service puppet ensure=stopped
   puppet resource service pe-puppetserver ensure=stopped
   puppet resource service pe-puppetdb ensure=stopped
   puppet resource service pe-console-services ensure=stopped
   puppet resource service pe-nginx ensure=stopped
   ~~~

2. When restoring the ‘pe-postgres’ user needs read access to the SQL file created when the database was backed up. Run the following command to perform the restore:

   ~~~sql
   sudo -u pe-postgres /opt/puppetlabs/server/apps/postgresql/bin/psql < <BACKUP_FILE>.sql
   ~~~

   This command will import the complete PE PostgreSQL database backup you created in the procedure above.

3. Start all PE services. Run the following commands:

   ~~~
   puppet resource service puppet ensure=running
   puppet resource service pe-puppetserver ensure=running
   puppet resource service pe-puppetdb ensure=running
   puppet resource service pe-console-services ensure=running
   puppet resource service pe-nginx ensure=running
   ~~~


### Individual Database Backup

>**Warning**: Although it is possible to back up individual PE PostgreSQL databases using the `pg_dump` command illustrated in this section, we recommend a full databases backup (as shown in the previous procedure) as the primary method for backing up your databases.  Backing up individual databases does not include access permissions; therefore, any PostgreSQL user will have access to these databases on restore, creating security issues.

**NOTE:** All back up and restore commands must be run as the pe-postgres user.

To backup an individual PE PostgreSQL database, run the following command:

~~~sql
sudo -u pe-postgres /opt/puppetlabs/server/apps/postgresql/bin/pg_dump -Fc <DATABASE_NAME> -f <BACKUP_FILE>.bin
~~~
This command creates a binary dump of the database.  The `pe-postgres` user needs write access to the output destination.



### Restore Individual Database Backup

**NOTE:** All back up and restore commands must be run as the pe-postgres user.
To restore an individual PE PostgreSQL database backup, run the following command:


~~~sql
sudo -u pe-postgres /opt/puppetlabs/server/apps/postgresql/bin/pg_restore -Cc -d template1 <BACKUP_FILE>.bin
~~~

This command will connect to the `template1` database and then drop the database indicated in <BACKUP_FILE>.bin before re-creating that database and connecting to the newly created database to perform the restoration.

If you omit the `-d` parameter, `pg_restore` will output the SQL commands needed to restore a database to the console, but **it will not perform the actual restoration**.

Continue to [Fixing Database Ownership](#fixing-database-ownership), as you **MUST** fix database access privileges and ownership after performing this procedure.

#### Fixing Database Ownership

If you use the `pg_dump` command to backup an individual database, after restoring that database, you will need to manually fix access privileges for that database. This can be done with a simple SQL file and command (this will correct all the PE databases).

To fix access privileges and ownership:

1. Create an SQL file (e.g., `access.sql`) and edit it to contain the following content:

   ~~~sql
   REVOKE ALL ON DATABASE "pe-activity" FROM PUBLIC;
   REVOKE ALL ON DATABASE "pe-activity" FROM "pe-postgres";
   GRANT ALL ON DATABASE "pe-activity" TO "pe-postgres";
   GRANT TEMPORARY ON DATABASE "pe-activity" TO PUBLIC;
   GRANT ALL ON DATABASE "pe-activity" TO "pe-activity";
   REVOKE ALL ON DATABASE "pe-classifier" FROM PUBLIC;
   REVOKE ALL ON DATABASE "pe-classifier" FROM "pe-postgres";
   GRANT ALL ON DATABASE "pe-classifier" TO "pe-postgres";
   GRANT TEMPORARY ON DATABASE "pe-classifier" TO PUBLIC;
   GRANT ALL ON DATABASE "pe-classifier" TO "pe-classifier";
   REVOKE ALL ON DATABASE "pe-puppetdb" FROM PUBLIC;
   REVOKE ALL ON DATABASE "pe-puppetdb" FROM "pe-postgres";
   GRANT ALL ON DATABASE "pe-puppetdb" TO "pe-postgres";
   GRANT TEMPORARY ON DATABASE "pe-puppetdb" TO PUBLIC;
   GRANT ALL ON DATABASE "pe-puppetdb" TO "pe-puppetdb";
   REVOKE ALL ON DATABASE "pe-rbac" FROM PUBLIC;
   REVOKE ALL ON DATABASE "pe-rbac" FROM "pe-postgres";
   GRANT ALL ON DATABASE "pe-rbac" TO "pe-postgres";
   GRANT TEMPORARY ON DATABASE "pe-rbac" TO PUBLIC;
   GRANT ALL ON DATABASE "pe-rbac" TO "pe-rbac";
   REVOKE ALL ON DATABASE template1 FROM PUBLIC;
   REVOKE ALL ON DATABASE template1 FROM "pe-postgres";
   GRANT ALL ON DATABASE template1 TO "pe-postgres";
   GRANT CONNECT ON DATABASE template1 TO PUBLIC;
   ~~~

2. Run the following command:

   ~~~
   sudo -u pe-postgres -s /bin/bash -c '/opt/puppetlabs/bin/psql < /tmp/access.sql'
   ~~~

Changing the PuppetDB User/Password
-----

The console uses a database user account to access its PostgreSQL database. If this user's password is compromised, or if it needs to be changed periodically, do the following:

1. Stop the `pe-puppetdb` service with `puppet resource service pe-puppetdb ensure=stopped`.
2. On the database server (which may or may not be the same as PuppetDB, depending on your deployment's architecture) use the PostgreSQL administration tool of your choice to change the user's password. With the standard `psql` client, you can do this with `ALTER USER console PASSWORD '<new password>';`.
3. Edit `/etc/puppetlabs/puppetdb/conf.d/database.ini` on the PuppetDB server and change the `password:` line under __common__ (or under __production,__ depending on your configuration) to contain the new password.
4. Start the `pe-puppetdb` service on the console server with with `puppet resource service pe-puppetdb ensure=running`.

Changing PuppetDB’s Parameters
------------------------------

PuppetDB parameters are set in the `jetty.ini` file, which is contained in the pe-puppetdb module. Jetty.ini is managed by PE, so if you change any PuppetDB parameters directly in the file, those changes will be overwritten on the next Puppet run.

Instead, you should use the console to make changes to the parameters of the `pe-puppetdb` class. For example, the [PuppetDB performance dashboard](/puppetdb/1.6/maintain_and_tune.html) requires the `listen_address` parameter to be set to “0.0.0.0”. So, in the console, you would edit the `pe_puppetdb` class so that the value of the `listen_address` parameter is set to “0.0.0.0”.

> **Warning**: This procedure will enable insecure access to the PuppetDB instance on your server.

If you are unfamiliar with editing class parameters in the console, refer to [Editing Class Parameters on Nodes](/pe/latest/console_classes_groups_making_changes.html#editing-parameters).


* * *

- [Next: Troubleshooting the Installer](./trouble_install.html)
