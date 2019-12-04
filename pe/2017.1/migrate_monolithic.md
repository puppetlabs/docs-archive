---
layout: default
title: "Migrating to PE 2017.1"
subtitle: "Migrating from PE 3.8 to 2017.1: Monolithic"
canonical: "/pe/latest/migrate_monolithic.html"
---

If you are on PE 3.8, you must migrate to PE 2017.1, as upgrades from 3.8 are not supported. When you migrate your installation, you back up your 3.8 infrastructure, install PE 2017.1 on new machines, and transfer your infrastructure to the new version.

A migration consists of the following high-level steps:

1. Back up the SSL directory and databases on your 3.8 Puppet master, and transfer these backups to the new Puppet master server.
2. Restore the SSL directory on the new Puppet master server.
3. Install PE 2017.1 on the new Puppet master server.
4. Restore the databases; fix database permissions; and recreate database extensions on the 22017.1 Puppet master.
5. **(Optional)** Deactivate and clear the certificate from your 3.8 Puppet master.
6. Migrate your configuration files, as well as your Puppet code and Hiera data from your 3.8 Puppet master to the 2017.1 Puppet master.
7. Configure your Puppet agents to point at the new Puppet master


## Migrate from PE 3.8 to 2017.1

Before you begin your migration, read the following notes and warnings.

- You must have a new server available to become your new Puppet master. See the
[system requirements](./sys_req_os.html) for a list of supported platforms.
- Use the [catalog_preview module](https://forge.puppet.com/puppetlabs/catalog_preview)
to test your Puppet code against language changes made in PE 2016.2. See
[Preparing for Migration with the catalog_preview module](./migrate_pe_catalog_preview.html) for details on
testing your environment with this module. In addition, the [Puppet 3 to Puppet 4 upgrade guide](/upgrade/#your-guide-to-modernizing-your-infrastructure) contains useful information about about preparing your Puppet code for migrating from PE 3.8.x to later versions.
- This guide assumes you **are not** using an external PostgreSQL database. 
- Review major changes to configuration files and directories:

   - Last year in PE, we changed the locations of several important configuration files and directories. For a summary of the changes, see [major path changes in PE](./install_upgrading_path_changes.html).
   
   - [Migrating Puppet Enterprise: Important notes](./migrate_notes.html) contains information about other notable changes in PE since 3.8.

**To migrate your installation:**

1. **If the server you intend to be the new Puppet master was previously used as an agent node,** on your existing 3.8 Puppet master, run the following command:

   ~~~
   `puppet cert clean <CERTNAME OF SERVER TO BE REUSED AS NEW PUPPET MASTER>`
   ~~~

2. Back up the SSL directory and databases on your 3.8 Puppet master. Run the following command:

   >**Note**: The pe-postgres user must have write access to the output destination and read access to the SQL file created when you backup the database.
   
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

4. Restore the SSL directory on the new Puppet master server. Run the following commands:

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

5. Install PE 2017.1 on the new Puppet master server. Follow the [monolithic installation documentation](./install_pe_mono.html).

6. Restore the databases, fix database permissions, and recreate database extensions on the 2017.1 Puppet master. Run the following commands:

   >**Note:** In this step, you’ll also edit PE classification groups to reflect the 2017.1 Puppet master’s certificate name.

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
   #Repair PE classification groups to reflect the 2017.1 Puppet master’s certificate name.
   /opt/puppetlabs/bin/puppet-infrastructure configure
   ~~~

   **You can safely ignore the following errors:**

   ~~~
   pg_restore: [archiver (db)] Error while PROCESSING TOC:
   pg_restore: [archiver (db)] Error from TOC entry 5; 2615 2200 SCHEMA public pe-postgres
   pg_restore: [archiver (db)] could not execute query: ERROR:  schema "public" already exists
   Command was: CREATE SCHEMA public;
   ~~~


7. **(Optional)** Deactivate and clear the certificate from your 3.8 Puppet master. Run the following commands:


   >**Warning:** If your old master and new master share a certificate name you should not complete this step.  

   ~~~
   OLD_MASTER_CERTNAME=<3.8_PUPPET_MASTER_CERTNAME>
   puppet node deactivate $OLD_MASTER_CERTNAME
   puppet cert clean $OLD_MASTER_CERTNAME
   ~~~


8. Migrate your configuration files, Puppet code, and Hiera data.

   a. Manually update settings in `puppet.conf` to use the [new defaults directories and configuration files in PE](./install_upgrading_path_changes.html).

     Configuration files for PE are still located in `/etc/puppetlabs`.
     
   b. Migrate your Puppet code to `/etc/puppetlabs/code/environments`.
    
      - If you use r10k, place your existing `r10k.yaml` into `/etc/puppetlabs/r10k/`, and modify it so that the `basedir` is set to `/etc/puppetlabs/code/environments/` and the `cachedir` is set to `/etc/puppetlabs/server/data/r10k`.

      - If you're not using r10k, copy your existing code into `/etc/puppetlabs/code/environments`.

   c. Migrate your Hiera data.
   
      - Put your existing `hiera.yaml` file in `/etc/puppetlabs/puppet/hiera.yaml` and update the `datadir` to the new default, `/etc/puppetlabs/code/environments/%{environment}/hieradata`.

   d. **(Optional)** If you set up a third-party certificate for your console, re-configure it according to the [custom console cert](./custom_console_cert.html) documentation.

      >**Note**: Nginx has replaced Apache as the PE webserver. This change was made to enhance security and performance. This change should be mostly transparent to users, except for the following:
      >
      >* The webserver user changes from pe-apache (pe-httpd) to pe-webserver.
      >* The webserver service changes from pe-httpd to pe-nginx.

9. Configure your Puppet agents to point at the new Puppet master.

   We recommend upgrading agents to 2017.1. See [Upgrading Puppet Agents](./install_upgrading_agents.html) for details.

   Alternatively, you can simply update the `puppet.conf` file for each agent to point it to the new Puppet master.

### Migrating from Split to Monolithic?

If you are migrating from a split install to a monolithic install then you only need one modification to this document in order to achieve your goal.

In [step 1](#step-1-back-up-38-ssl-directory-and-databases), backup the SSL directory on the Puppet master of your split install and backup the databases on the PuppetDB node of your split install.  Beyond that all of the restore steps remain the same.
