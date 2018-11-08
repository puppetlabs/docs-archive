---
layout: default
title: "PE 3.8 » Maintenance » Console"
subtitle: "Maintaining the Console & Databases"
canonical: "/pe/latest/maintain_console-db.html"
---


If PE's console becomes sluggish or begins taking up too much disk space, there are several maintenance tasks that can improve its performance.

Pruning the Console Database with a Cron Job
-------------

For new PE installs (3.8 and later), a cron job, managed by a class in the `puppetlabs-pe_console_prune` module, is installed that will prevent bloating in the console database by deleting old data (mainly uploaded Puppet run reports) after a set number of days. You can tweak the parameters of this class as needed, primarily the `prune_upto` parameter, which sets the time to keep records in the database. This parameter is set to 30 days by default.

However, to prevent users from deleting data without notice, the cron job is not installed on upgrades from versions earlier than 3.8.

To prevent bloating in the console database, we recommend adding the `pe_console_prune` class to the `PE Console` group after upgrading to PE 3.8.

To access the `prune_upto` parameter:

1. In the PE console, click **Classification**.

2. Select the `PE Console` group.

3. Click **Classes**.

4. Under the `pe_console_prune` class, edit the parameters as needed.



Restarting the Background Tasks
-----

The console uses several worker services to process reports in the background, and it displays a running count of pending tasks in the upper left corner of the interface:

![The background tasks box with one pending task][maint_pending_task]

[maint_pending_task]: ./images/console/maint_pending_task.png

If the number of pending tasks appears to be growing linearly, the background task processes may have died and left invalid PID files. To restart the worker tasks, run:

    $ sudo /etc/init.d/pe-puppet-dashboard-workers restart

The number of pending tasks shown in the console should start decreasing rapidly after restarting the workers.


Optimizing the Database
-----

> **Note**: The following task is not available in PE 3.8. Refer to the [PostgreSQL workaround](./release_notes_known_issues.html#dbrawoptimize-rake-task-does-not-work-in-pe-37x) provided in the Known Issues.

PostgreSQL should have `autovacuum=on` set by default. If you're having issues with the database growing too large and unwieldy, make sure this setting did not get turned off. In most cases, this should suffice. In some cases, more heavyweight maintenance measures may be needed (e.g. in cases of data corruption from hardware failures). To help with this, PE provides a rake task that performs advanced database maintenance.

This task, `rake db:raw:optimize[mode]`,  runs in three modes:

  * `reindex` mode will run the REINDEX DATABASE command on the console database. This is also the default mode if no mode is specified.
  * `vacuum` model will run the VACUUM FULL command on the console database.
  * `reindex+vacuum` will run both of the above commands on the console database.

To run the task, change your working directory to `/opt/puppet/share/puppet-dashboard` and make sure your PATH variable contains `/opt/puppet/bin` (or use the full path to the rake binary). Then run the task `rake db:raw:optimize[mode]`. You can disregard any error messages about insufficient privileges to vacuum certain system objects because these objects should not require vacuuming. If you believe they do, you can do so manually by logging in to psql (or your tool of choice) as a database superuser.

Please note that you should have at least as much free space available as is currently in use, on the partition where your postgresql data is stored, prior to attempting a full vacuum. If you are using the PE-vendored postgresql, the postgres data is kept in `/opt/puppet/var/lib/pgsql/`.

The PostgreSQL docs contain more detailed information about [vacuuming](http://www.postgresql.org/docs/9.2/static/routine-vacuuming.html) and [reindexing](http://www.postgresql.org/docs/9.2/static/sql-reindex.html).


Cleaning Old Reports
----------------

Agent node reports will build up over time in the console's database. If you wish to delete the oldest reports for performance, storage, or policy reasons, you can use the `reports:prune` rake task.

For example, to delete reports more than one month old:

    $ sudo /opt/puppet/bin/rake \
    -f /opt/puppet/share/puppet-dashboard/Rakefile \
    RAILS_ENV=production \
    reports:prune upto=1 unit=mon

Although this task [**should be run regularly as a cron job,**](#Pruning_the_console_database_with_a_cron_job) the actual frequency at which you set it to run will depend on your site's policies.

If you run the `reports:prune` task without any arguments, it will display further usage instructions. The available units of time are `yr`, `mon`, `wk`, `day`, `hr`, and `min`.

Database Backups
----------------

Puppet Enterprise uses PostgreSQL as a database backend. We recommend that you use PostgreSQL’s native tools to perform database exports and imports.  At a minimum, we recommend nightly backups to a remote system, or as dictated by your company policy.

The PE PostgreSQL database (in versions 3.7 and 3.8) includes the following databases: 

Database      | Description
------------- | ---------------------------------------------------------------
console       | Console data, including reports and classification information
pe-activity   | Activity data from the Classifier, including who, what and when
pe-classifier | Classification data, all Node Group information
pe-puppetdb   | PuppetDB’s data, including exported resources, catalogs, facts, and reports
pe-rbac       | RBAC data, including users, permissions, and AD/LDAP info

## Backup and Restore Procedures for the PE Databases

### Complete Database Backup

**NOTE:** All back up and restore commands must be run as the pe-postgres user.

To back up the entire PE PostgreSQL database, run the following command:

~~~sql
 sudo -u pe-postgres /opt/puppet/bin/pg_dumpall -c -f <BACKUP_FILE>.sql
~~~

>**Note**: The name and location of your <\BACKUP_FILE>.sql can be changed as needed. 

This command, using `pg_dumpall`, dumps the databases to an SQL file on disk.  The `pe-postgres` user needs write access to the output destination.  

**Note**: It isn’t necessary to stop any PE services to perform these steps.

### Restore Complete Database Backup

To restore the entire PE PostgreSQL database backup:

1. Since all databases will be destroyed and re-created in this procedure, you must first stop all PE services except for `pe-postgres`. Run the following commands:

   ~~~
   puppet resource service pe-puppet ensure=stopped
   puppet resource service pe-puppetserver ensure=stopped
   puppet resource service pe-puppetdb ensure=stopped
   puppet resource service pe-console-services ensure=stopped
   puppet resource service pe-httpd ensure=stopped
   puppet resource service pe-puppet-dashboard-workers ensure=stopped
   ~~~
   
2. When restoring the ‘pe-postgres’ user needs read access to the SQL file created when the database was backed up. Run the following command to perform the restore:

   ~~~sql
   sudo -u pe-postgres /opt/puppet/bin/psql < <BACKUP_FILE>.sql
   ~~~
   
   This command will import the complete PE PostgreSQL database backup you created in the procedure above.
   
3. Start all PE services. Run the following commands:

   ~~~
   puppet resource service pe-puppet ensure=running
   puppet resource service pe-puppetserver ensure=running
   puppet resource service pe-puppetdb ensure=running
   puppet resource service pe-console-services ensure=running
   puppet resource service pe-httpd ensure=running
   puppet resource service pe-puppet-dashboard-workers ensure=running
   ~~~


### Individual Database Backup

>**Warning**: Although it is possible to back up individual PE PostgreSQL databases using the `pg_dump` command illustrated in this section, we recommend a full databases backup (as shown in the previous procedure) as the primary method for backing up your databases.  Backing up individual databases does not include access permissions; therefore, any PostgreSQL user will have access to these databases on restore, creating security issues.

**NOTE:** All back up and restore commands must be run as the pe-postgres user.

To backup an individual PE PostgreSQL database, run the following command:

~~~sql
sudo -u pe-postgres /opt/puppet/bin/pg_dump -Fc <DATABASE_NAME> -f <BACKUP_FILE>.bin
~~~
This command creates a binary dump of the database.  The `pe-postgres` user needs write access to the output destination.  



### Restore Individual Database Backup

**NOTE:** All back up and restore commands must be run as the pe-postgres user.
To restore an individual PE PostgreSQL database backup, run the following command:

To restore an individual PE PostgreSQL database backup, run the following command:

~~~sql
sudo -u pe-postgres /opt/puppet/bin/pg_restore -Cc -d template1 <BACKUP_FILE>.bin
~~~

This command will connect to the `template1` database and then drop the database indicated in <BACKUP_FILE>.bin before re-creating that database and connecting to the newly created database to perform the restoration.

If you omit the `-d` parameter, `pg_restore` will output the SQL commands needed to restore a database to the console, but **it will not perform the actual restoration**.

Continue to [Fixing Database Ownership](#fixing-database-ownership), as you **MUST** fix database access privileges and ownership after performing this procedure.

#### Fixing Database Ownership
 
If you use the `pg_dump` command to backup an individual database, after restoring that database, you will need to manually fix access privileges for that database. This can be done with a simple SQL file and command (this will correct all the PE databases). 
 
To fix access privileges and ownership:

1. Create an SQL file (e.g., `access.sql`) and edit it to contain the following content:

   ~~~sql
   REVOKE ALL ON DATABASE console FROM PUBLIC;
   REVOKE ALL ON DATABASE console FROM "pe-postgres";
   GRANT ALL ON DATABASE console TO "pe-postgres";
   GRANT TEMPORARY ON DATABASE console TO PUBLIC;
   GRANT ALL ON DATABASE console TO console;
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
   sudo -u pe-postgres -s /bin/bash -c '/opt/puppet/bin/psql < /tmp/access.sql'
   ~~~

Changing the Console's Database User/Password
-----

The console uses a database user account to access its PostgreSQL database. If this user's password is compromised, or if it needs to be changed periodically, do the following:

1. Stop the `pe-httpd` service on the console server:

        $ sudo /etc/init.d/pe-httpd stop
2. On the database server (which may or may not be the same as the console, depending on your deployment's architecture) use the PostgreSQL administration tool of your choice to change the user's password. With the standard `psql` client, you can do this with:

        ALTER USER console PASSWORD '<new password>';
3. Edit `/etc/puppetlabs/puppet-dashboard/database.yml` on the console server and change the `password:` line under __common__ (or under __production,__ depending on your configuration) to contain the new password.
4. Start the `pe-httpd` service on the console server:

        $ sudo /etc/init.d/pe-httpd start

The same procedure is also used for the PuppetDB user's password, except you'll edit `/etc/puppetlabs/puppetdb/conf.d/database.ini` and will restart the `pe-puppetdb` service.

Changing PuppetDB’s Parameters
------------------------------

PuppetDB parameters are set in the `jetty.ini` file, which is contained in the pe-puppetdb module. Jetty.ini is managed by PE, so if you change any PuppetDB parameters directly in the file, those changes will be overwritten on the next Puppet run.

Instead, you should use the console to make changes to the parameters of the `pe-puppetdb` class. For example, the [PuppetDB performance dashboard](/puppetdb/1.6/maintain_and_tune.html) requires the `listen_address` parameter to be set to “0.0.0.0”. So, in the console, you would edit the `pe_puppetdb` class so that the value of the `listen_address` parameter is set to “0.0.0.0”.

> **Warning**: This procedure will enable insecure access to the PuppetDB instance on your server.

If you are unfamiliar with editing class parameters in the console, refer to [Editing Class Parameters on Nodes](/pe/latest/console_classes_groups_making_changes.html#editing-parameters).


* * *

- [Next: Troubleshooting the Installer](./trouble_install.html)
