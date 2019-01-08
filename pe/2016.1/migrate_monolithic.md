---
layout: default
title: "Migrating to PE 2016.1"
subtitle: "Migrating from PE 3.8 to PE 2016.1: Monolithic"
canonical: "/pe/latest/migrate_monolithic.html"
---

This guide describes the steps for migrating Puppet Enterprise from version 3.8 to 2016.1 in a monolithic installation. For instructions on migrating *to* PE 3.8.x from earlier versions, including instructions on using the node classification migration tool, see the [PE 3.8.x upgrade docs](http://docs.puppetlabs.com/pe/3.8/install_upgrading.html).

This process doesn't apply to split installations, where the Puppet master, console, and PuppetDB components are installed on different machines.

In most situations, you can [upgrade from PE 3.8.x to PE 2016.1](./install_upgrading.html), which is a less complicated process. However, in high availability environments, you can reduce downtime by migrating. Migrating involves moving your master from one machine to another.

## Before You Begin

You must have a new machine available to become your new Puppet master. See the
[system requirements](./install_system_requirements.html) for details on supported platforms.

Use the [catalog_preview module](https://forge.puppetlabs.com/puppetlabs/catalog_preview)
to test your Puppet code against language changes made in Puppet. See
[Preparing for Migration to PE 2016.1](./migrate_pe_catalog_preview.html) for details on
testing your environment with this module.

### Puppet 3 to Puppet 4 upgrade guide

In addition, the [Puppet 3 to Puppet 4 upgrade guide](/upgrade/#your-guide-to-modernizing-your-infrastructure) contains useful information about about preparing your Puppet code for migrating from PE 3.8.x to later versions. 

>**Note**: You must run all backup and restore commands as the pe-postgres user, with
write access to the output destination and read access to the SQL file created when you
back up the database.

### Step 1: Back up the PE 3.8.x SSL directory and transfer it to a new machine

Transferring your SSL directory enables the installer to use your existing certificates when you install PE 2016.1.

>**Note**: Complete this step before installing PE on the new Puppet master machine.

1. On your existing 3.8.x Puppet master, back up the SSL directory `/etc/puppetlabs/puppet/ssl`.

2. On the new machine:

   a. Run `mkdir -p /etc/puppetlabs/puppet`.

   b. Copy the 3.8.x SSL directory to that path. The path of your new ssl directory must match the path of your original directory: `/etc/puppetlabs/puppet/ssl`.

### Step 2: Back up the PE 3.8.x databases

Perform a complete back up of the PE 3.8.x databases so that you can restore them later in your upgraded environment.

It isn't necessary to stop PE services to perform this step.

>**Note**: Changes made on the old system between this step and step 8, when you point agents to the new Puppet master, aren't migrated. We recommend communicating your migration schedule to users so that any updates -- like reports for runs, classification changes, or parameter updates -- are transferred to the new system.

* On your existing 3.8.x Puppet master, run these commands, where `/tmp/` can be a location of your choice:

  ~~~
  sudo -u pe-postgres /opt/puppet/bin/pg_dump -Fc pe-puppetdb -f /tmp/pe-puppetdb.backup.bin
  sudo -u pe-postgres /opt/puppet/bin/pg_dump -Fc pe-classifier -f /tmp/pe-classifier.backup.bin
  sudo -u pe-postgres /opt/puppet/bin/pg_dump -Fc pe-rbac -f /tmp/pe-rbac.backup.bin
  sudo -u pe-postgres /opt/puppet/bin/pg_dump -Fc pe-activity -f /tmp/pe-activity.backup.bin
  ~~~

### Step 3: Install PE 2016.1 on the new machine

Install PE on the new machine that you transferred your SSL directory to. See [Installing Puppet Enterprise: Monolithic](./install_pe_mono.html) for details.

### Step 4: Restore the PE databases

On your new Puppet master machine, restore existing databases to preserve your settings.

1. Stop all PE services except `pe-postgresql` by running these commands:

   ~~~
   puppet resource service puppet ensure=stopped
   puppet resource service pe-puppetserver ensure=stopped
   puppet resource service pe-puppetdb ensure=stopped
   puppet resource service pe-console-services ensure=stopped
   puppet resource service pe-nginx ensure=stopped
   ~~~

2. Copy each of the database backup files that you created in step 2 to your new machine.

3. On the new machine, restore the databases by running these commands, where `/tmp/` is the location of your backup file:

   ~~~
   sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_restore -Cc /tmp/pe-puppetdb.backup.bin -d template1
   sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_restore -Cc /tmp/pe-classifier.backup.bin -d template1
   sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_restore -Cc /tmp/pe-activity.backup.bin -d template1
   sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_restore -Cc /tmp/pe-rbac.backup.bin -d template1
   ~~~

   You can ignore these errors if they appear:

   ~~~
   pg_restore: [archiver (db)] Error while PROCESSING TOC:
   pg_restore: [archiver (db)] Error from TOC entry 5; 2615 2200 SCHEMA public pe-postgres
   pg_restore: [archiver (db)] could not execute query: ERROR:  schema "public" already exists
   Command was: CREATE SCHEMA public;
   ~~~

4. Set up grant tables in the new database. In an interactive psql session or from a separate sql input file, run these commands:

   ~~~
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
   
   echo '\c pe-puppetdb;
   CREATE EXTENSION pg_trgm;
   CREATE EXTENSION pgcrypto;' > $BACKUP_DIR/recreate_extensions.sql
   
   sudo -u pe-postgres /opt/puppetlabs/server/bin/psql -f $BACKUP_DIR/recreate_extensions.sql
   ~~~

5. Start all PE services except puppet by running these commands:

   ~~~
   puppet resource service pe-puppetserver ensure=running
   puppet resource service pe-puppetdb ensure=running
   puppet resource service pe-console-services ensure=running
   puppet resource service pe-nginx ensure=running
   ~~~

### Step 5: Deactivate your old master certname from PuppetDB

Because you copied the certs and database from an old machine, you must deactivate the cert on the new machine.

* On the new Puppet master machine, deactivate the old certname by running these commands:

  ~~~
  puppet node deactivate <OLD_MASTER_CERTNAME>
  puppet cert clean <OLD_MASTER_CERTNAME>
  ~~~

### Step 6: Reconfigure PE classification and start puppet

Restoring the pe-classifier database reverts to PE 3.8.x classification, so you must update to PE 2016.1 classification before starting Puppet.

1. Remove the pinned node for the old master and pin your new master node for these groups:
   * PE ActiveMQ Broker
   * PE Certificate Authority
   * PE Console
   * PE Master
   * PE PuppetDB

2. Edit the rule from `pe_version matches regex .+` to `aio_agent_version matches regex .+` for these groups:
   * PE Agent
   * PE Mcollective

3. On *PE Infrastructure* in the *Classes* tab, change the parameter values from your old master certname to your new master certname:
   * `mcollective_middleware_hosts` -- Formatted as an array surrounded by brackets [ ].
   * `database_host`
   * `puppetdb_host`
   * `puppet_master_host`
   * `certificate_authority_host`
   * `console_host`

4. Check your updates by running `puppet agent -t --noop`.

5. Start the Puppet Agent on your new master by running `puppet resource service puppet ensure=running`.

### Step 7: Migrate your configurations, Puppet Code, and Hiera data

1. Manually update settings in `puppet.conf` to use the [new defaults directories and configuration files in PE](./install_upgrading_path_changes.html).

   Configuration files for PE are still located in `/etc/puppetlabs`.

2. Migrate your Puppet code to `/etc/puppetlabs/code/environments`.
    - If you use r10k, place your existing `r10k.yaml` into `/etc/puppetlabs/r10k/`, and     modify it so that the `basedir` is set to `/etc/puppetlabs/code/environments/` and the `cachedir` is set to `/etc/puppetlabs/server/data/r10k`.

    - If you're not using r10k, copy your existing code into `/etc/puppetlabs/code/environments`.

3. Migrate your Hiera data.
   - If you use r10k and keep your Hiera data in your control repo, put your existing `hiera.yaml` file in `/etc/puppetlabs/code/hiera.yaml` and update the `datadir` to the new default, `/etc/puppetlabs/code/environments/%{environment}/hieradata`.

   - If you're not using r10k, put your existing `hiera.yaml` file in `/etc/puppetlabs/code/hiera.yaml`, update the `datadir` to `/etc/puppetlabs/code/hieradata`, and copy your existing Hiera data into that directory.

4. (Optional) If you set up a third-party certificate for your console, reconfigure it according to the [custom console cert](./custom_console_cert.html) documentation.

   >**Note**: [The console now uses nginx instead of Apache](./install_upgrading_notes.html#nginx-replaces-apache-as-the-puppet-enterprise-web-server).

### Step 8: Point agents to the new Puppet master

We recommend upgrading agents to 2016.1. See [Upgrading Puppet Agents](./install_upgrading_agents.html) for details.

Alternatively, you can simply update the puppet.conf file for each agent to point it to the new Puppet master.