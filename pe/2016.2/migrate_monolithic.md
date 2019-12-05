---
layout: default
title: "Migrating to PE 2016.2"
subtitle: "Migrating from PE 3.8 to 2016.2: Monolithic"
canonical: "/pe/latest/migrate_monolithic.html"
---

This guide describes the steps for migrating Puppet Enterprise from version 3.8 to 2016.2 in a monolithic installation. For instructions on migrating *to* PE 3.8.x from earlier versions, including instructions on using the node classification migration tool, see the [PE 3.8.x upgrade docs](/pe/3.8/install_upgrading.html).

This assumes you **are not** using an external PostgreSQL database.

## Before you begin your migration

Read the following notes and warnings before you begin your migration.

- You must have a new server available to become your new Puppet master. See the
[system requirements](./install_system_requirements.html) for a list of supported platforms.   
- Use the [catalog_preview module](https://forge.puppet.com/puppetlabs/catalog_preview)
to test your Puppet code against language changes made in PE 2016.2. See
[Preparing for Migration to PE 2016.2](./migrate_pe_catalog_preview.html) for details on
testing your environment with this module. In addition, the [Puppet 3 to Puppet 4 upgrade guide](/upgrade/#your-guide-to-modernizing-your-infrastructure) contains useful information about about preparing your Puppet code for migrating from PE 3.8.x to later versions. 

### Review major changes to configuration files and directories

Last year in PE, we changed the locations of several important configuration files and directories. For a summary of the changes, see [major path changes in PE](./install_upgrading_path_changes.html).

### Review migration notes and warnings

[Migrating Puppet Enterprise: Important notes](./migrate_notes.html) contains information about other notable changes in PE since 3.8.

### Migrating from Split to Monolithic?

If you are migrating from a split install to a monolithic install then you only need one modification to this document in order to achieve your goal.

In [step 1](#step-1-back-up-38-ssl-directory-and-databases), backup the SSL directory on the Puppet master of your split install and backup the databases on the PuppetDB node of your split install.  Beyond that all of the restore steps remain the same.

### Migration overview

This documentation provides commands you can run for your migration. A migration consists of the following high-level steps:

1. Back up the SSL directory and databases on your 3.8 Puppet master, and transfer these backups to the new Puppet master server.
2. Restore the SSL directory on the new Puppet master server.
3. Install PE 2016.2 on the new Puppet master server.
4. Restore the databases; fix database permissions; and recreate database extensions on the 2016.2 Puppet master.
5. **(Optional)** Deactivate and clear the certificate from your 3.8 Puppet master.
6. Migrate your configuration files, as well as your Puppet code and Hiera data from your 3.8 Puppet master to the 2016.2 Puppet master.
7. Configure your Puppet agents to point at the new Puppet master


### Step 1: Back up 3.8 SSL directory and databases

In this step, you'll back up the SSL directory and databases on your 3.8 Puppet master.

>**Note**: The pe-postgres user must have write access to the output destination and read access to the SQL file created when you backup the database.

1. **If the server you intend to be the new Puppet master was previously used as an agent node,** on your existing 3.8 Puppet master, run the following command:

   ~~~
   `puppet cert clean <CERTNAME OF SERVER TO BE REUSED AS NEW PUPPET MASTER>`
   ~~~

2. To start the back up, run the following command:

   >**Note:** If you don’t want to use `/tmp/backup`, you will need to change the `BACKUP_DIR` variable throughout the remaining procedures.

   ~~~
   BACKUP_DIR=/tmp/backup

   tar -zcvf $BACKUP_DIR/puppet_ssl.tar.gz /etc/puppetlabs/puppet/ssl/

   chown pe-postgres:pe-postgres $BACKUP_DIR
   sudo -u pe-postgres /opt/puppet/bin/pg_dump -Fc pe-puppetdb -f $BACKUP_DIR/pe-puppetdb.backup.bin
   sudo -u pe-postgres /opt/puppet/bin/pg_dump -Fc pe-classifier -f $BACKUP_DIR/pe-classifier.backup.bin
   sudo -u pe-postgres /opt/puppet/bin/pg_dump -Fc pe-rbac -f $BACKUP_DIR/pe-rbac.backup.bin
   sudo -u pe-postgres /opt/puppet/bin/pg_dump -Fc pe-activity -f $BACKUP_DIR/pe-activity.backup.bin
   ~~~

3. Use your preferred method to transfer these back ups to the new Puppet master server.

### Step 2: Restore SSL directory on new Puppet master

In this step, you'll restore the SSL directory on the new Puppet master server.

- Run the following commands:

  ~~~
  BACKUP_DIR=/tmp/backup

  mkdir -p /etc/puppetlabs/puppet

  tar -zxvf $BACKUP_DIR/puppet_ssl.tar.gz -C /

  #Removing PE internal certs so they can be regenerated
  rm -f /etc/puppetlabs/puppet/ssl/certs/pe-internal-classifier.pem
  rm -f /etc/puppetlabs/puppet/ssl/certs/pe-internal-dashboard.pem
  rm -f /etc/puppetlabs/puppet/ssl/private_keys/pe-internal-classifier.pem
  rm -f /etc/puppetlabs/puppet/ssl/private_keys/pe-internal-dashboard.pem
  rm -f /etc/puppetlabs/puppet/ssl/public_keys/pe-internal-classifier.pem
  rm -f /etc/puppetlabs/puppet/ssl/public_keys/pe-internal-dashboard.pem
  rm -f /etc/puppetlabs/puppet/ssl/ca/signed/pe-internal-classifier.pem
  rm -f /etc/puppetlabs/puppet/ssl/ca/signed/pe-internal-dashboard.pem
  ~~~

### Step 3: Install PE 2016.2

In this step, you'll install PE 2016.2 on the new Puppet master server.

Follow the [installation documentation](./install_basic.html) to install PE 2016.2.

### Step 4: Restore the databases

In this step, you'll restore the databases, fix database permissions, and recreate database extensions on the 2016.2 Puppet master.

>**Note:** In this step, you’ll also edit PE classification groups to reflect the 2016.2 Puppet master’s certificate name.

- Run the following commands:

  ~~~
  BACKUP_DIR=/tmp/backup

  puppet resource service puppet ensure=stopped
  puppet resource service pe-puppetserver ensure=stopped
  puppet resource service pe-puppetdb ensure=stopped
  puppet resource service pe-console-services ensure=stopped
  puppet resource service pe-nginx ensure=stopped
  puppet resource service pe-activemq ensure=stopped
  puppet resource service pe-orchestration-services ensure=stopped
  puppet resource service pxp-agent ensure=stopped

  sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_restore -Cc $BACKUP_DIR/pe-puppetdb.backup.bin -d template1
  sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_restore -Cc $BACKUP_DIR/pe-classifier.backup.bin -d template1
  sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_restore -Cc $BACKUP_DIR/pe-activity.backup.bin -d template1
  sudo -u pe-postgres /opt/puppetlabs/server/bin/pg_restore -Cc $BACKUP_DIR/pe-rbac.backup.bin -d template1

  #Start PE services
  #Install database extensions and repair database permissions
  #Repair PE classification groups to reflect the 2016.2 Puppet master’s certificate name.
  puppet enterprise configure
  ~~~

  **You can safely ignore the following errors:**

  ~~~
  pg_restore: [archiver (db)] Error while PROCESSING TOC:
  pg_restore: [archiver (db)] Error from TOC entry 5; 2615 2200 SCHEMA public pe-postgres
  pg_restore: [archiver (db)] could not execute query: ERROR:  schema "public" already exists
  Command was: CREATE SCHEMA public;
  ~~~


### Step 5: **(Optional)** Deactivate and clear the certificate from your 3.8 Puppet master

>**Warning:** If your old master and new master share a certificate name you should not complete this step.  

- Run the following commands:

  ~~~
  OLD_MASTER_CERTNAME=<3.8_PUPPET_MASTER_CERTNAME>
  puppet node deactivate $OLD_MASTER_CERTNAME
  puppet cert clean $OLD_MASTER_CERTNAME
  ~~~

### Step 6: Migrate your data

In this step, you'll migrate your configuration files, Puppet code, and Hiera data.

1. Manually update settings in `puppet.conf` to use the [new defaults directories and configuration files in PE](./install_upgrading_path_changes.html).

   Configuration files for PE are still located in `/etc/puppetlabs`.

2. Migrate your Puppet code to `/etc/puppetlabs/code/environments`.
    - If you use r10k, place your existing `r10k.yaml` into `/etc/puppetlabs/r10k/`, and modify it so that the `basedir` is set to `/etc/puppetlabs/code/environments/` and the `cachedir` is set to `/etc/puppetlabs/server/data/r10k`.

    - If you're not using r10k, copy your existing code into `/etc/puppetlabs/code/environments`.

3. Migrate your Hiera data.
   - Put your existing `hiera.yaml` file in `/etc/puppetlabs/puppet/hiera.yaml` and update the `datadir` to the new default, `/etc/puppetlabs/code/environments/%{environment}/hieradata`.

4. (Optional) If you set up a third-party certificate for your console, re-configure it according to the [custom console cert](./custom_console_cert.html) documentation.

   >**Note**: Nginx has replaced Apache as the PE webserver. This change was made to enhance security and performance. This change should be mostly transparent to users, except for the following:
   >
   >* The webserver user changes from pe-apache (pe-httpd) to pe-webserver.
   >* The webserver service changes from pe-httpd to pe-nginx.

### Step 7: Configure your Puppet agents to point at the new Puppet master

We recommend upgrading agents to 2016.2.x. See [Upgrading Puppet Agents](./install_upgrading_agents.html) for details.

Alternatively, you can simply update the `puppet.conf` file for each agent to point it to the new Puppet master.
