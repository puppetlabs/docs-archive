---
layout: default
title: "Back Up and Restore a Puppet Enterprise Installation"
canonical: "/pe/latest/maintain_backup_restore.html"
---

After you have PE installed, we recommend that you keep regular backups of your PE infrastructure. Regular backups allow you to recover from failed upgrades between versions, to troubleshoot those upgrades, and to quickly recover in the case of system failures. The instructions in this doc can also help you migrate your PE infrastructure from one set of nodes to another.

To perform a full backup and restore, you will:

1. [Back Up Your Database and Puppet Enterprise Files](#back-up-your-database-and-puppet-enterprise-files)
2. [Purge the Puppet Enterprise Installation (Optional)](#purge-the-puppet-enterprise-installation-optional)
3. [Restore Your Database and Puppet Enterprise Files](#restore-your-database-and-puppet-enterprise-files)

## Back Up Your Database and Puppet Enterprise Files

To properly back up your PE installation, the following databases and PE files should be backed up.

 - `/etc/puppetlabs/`---if you back up this directory to `/tmp/backups` you can later access your original installation answer file or `pe.conf` file when you restore. If you're restoring from a version earlier than 2016.2, you need to covert your answer file to [the pe.conf file format](./install_pe_conf_param.html).
 - The modulepath---if you've configured it to be outside the PE default of `modulepath = /etc/puppetlabs/code/environments/production/modules:/etc/puppetlabs/code/modules:/opt/puppetlabs/puppet/modules` in `puppet.conf`
 - `/opt/puppetlabs/server/data/console-services/certs/`
 - `/opt/puppetlabs/server/data/postgresql/9.4/data/certs/`
-  Ensure any SSH keys used for Code Manager are backed up. This key is stored in `/etc/puppetlabs/puppetserver/conf.d/code-manager.conf` in the `private-key` setting. If the file does not exist, Code Manager is not enabled, and this step is not needed.

[Backup and Restore Procedures for the PE Database](./maintain_console-db.html#backup-and-restore-procedures-for-the-pe-databases) provides instructions for backing up your PE PostgreSQL database.

> **Note**: If you have any custom Simple RPC agents, you will want to back these up. These are located in the `libdir` configured in `/etc/puppetlabs/mcollective/server.cfg`.

On a monolithic (all-in-one) install, the databases and PE files will all be located on the same node as the Puppet master.

On a split install (master, console, PuppetDB/PostgreSQL each on a separate node), they will be located across the various servers assigned to these PE components.

   - `/etc/puppetlabs/`: different versions of this directory can be found on the server assigned to the Puppet master component, the server assigned to the console component, and the server assigned to the PuppetDB/PostgreSQL component. You should back up each version.
   - `/opt/puppetlabs/server/data/console-services/certs/`: located on the server assigned to the console component.
   - `/opt/puppetlabs/server/data/postgresql/9.4/data/certs/`: located on the server assigned the PuppetDB/PostgreSQL component.
  - `/etc/puppetlabs/puppetserver/conf.d/code-manager.conf`: located on the Puppet master.
  - The pe-activity, pe-classifier, pe-orchestrator, pe-puppetdb, and pe-rbac databases: located on the server assigned to the PuppetDB/PostgreSQL component.

## Purge the Puppet Enterprise Installation (Optional)

If you're planning on restoring your databases and PE files to the same server(s), you'll want to first fully purge your existing Puppet Enterprise installation.

You'll need to run the uninstaller script `puppet-enterprise-uninstaller` with the `-p` and `-d` flags. It can be found either at `/opt/puppetlabs/bin/puppet-enterprise-uninstaller` or inside the installer tarball you downloaded when setting up PE initially.

> **Important**: If you have a split install, you will need to run the uninstaller on each server that has been assigned a component.

After running the uninstaller, ensure that `/opt/puppetlabs/` and `/etc/puppetlabs/` are no longer present on the system.

For more information about using the PE uninstaller, refer to [Uninstalling Puppet Enterprise](./install_uninstalling.html).

## Restore Your Database and Puppet Enterprise Files

1. Using the standard install process (run the `puppet-enterprise-installer` script), reinstall the same version of Puppet Enterprise that was installed for the files you backed up.

   - If you're restoring from a version earlier than 2016.2, you need to covert your answer file to [the pe.conf file format](./install_pe_conf_param.html), and use that during the installation process.

   - If you need to review the PE installation process, check out [Installing Puppet Enterpise](./install_basic.html).

2. Stop all PE services, except `pe-postgresql`. Run the following commands:

   a. On the Puppet master:

   ~~~
   puppet resource service puppet ensure=stopped
   puppet resource service pe-puppetserver ensure=stopped
   puppet resource service pe-orchestration-services ensure=stopped
   puppet resource service pe-nginx ensure=stopped
   ~~~

   b. On a monolithic install, run **only the first command** on the Puppet master; for a split install, run **both commands** on the node assigned the PuppetDB/PostgreSQL component:

   ~~~
   puppet resource service pe-puppetdb ensure=stopped
   puppet resource service puppet ensure=stopped
   ~~~

   c. On a monolithic install, run **only the first command** on the Puppet master; for a split install, run **both commands** on the node assigned the PE console component:

   ~~~
   puppet resource service pe-console-services ensure=stopped
   puppet resource service puppet ensure=stopped
   ~~~

3. Restore your PE PostgreSQL databases. [Backup and Restore Procedures for the PE Database](./maintain_console-db.html#backup-and-restore-procedures-for-the-pe-databases) provides instructions for restoring your PE PostrgreSQL database. (**Note**: Don't perform the steps to stop and start PE services when restoring the PE database.)

4. Restore from your `/etc/puppetlabs/` backup the following directories and files:

   For a monolithic install, these files should all be replaced on the Puppet master:

   - `/etc/puppetlabs/puppet/puppet.conf`
   - `/etc/puppetlabs/puppet/ssl` (fully replace with backup, do not leave existing ssl data)
   - `/etc/puppetlabs/puppetdb/ssl` (fully replace with backup, do not leave existing ssl data)
   - `/opt/puppetlabs/server/data/postgresql/9.4/data/certs/`
   - `/opt/puppetlabs/server/data/console-services/certs/`
   - The modulepath---if you've configured it to be something other than the PE default.

   For a split install, these files and databases should be replaced on the various servers assigned to these PE components.

   - `/etc/puppetlabs/`: as noted earlier, there is a different version of this directory for the Puppet master component, the console component, and the database support component (i.e., PuppetDB and PostgreSQL). You should replace each version.
   - `/etc/puppetlabs/puppetdb/ssl`: located on the server assigned to the PuppetDB component.
   - `/opt/puppetlabs/server/data/console-services/certs/`: located on the server assigned to the console component.
   - `/opt/puppetlabs/server/data/postgresql/9.4/data/certs/`: located on the server assigned the PuppetDB/PostgreSQL component.
   - The pe-classifier, pe-rbac, and pe-activity databases: located on the server assigned to the database support component.
   - The PuppetDB database: located on the server assigned to the database support component.
   - The modulepath: located on the server assigned to assigned to the Puppet master component.

   > **Note**: If you backed up any Simple RPC agents, you will need to restore these on the same server assigned to the Puppet master component.

5. Remove the cached catalog on the Puppet master:

   ~~~
   rm -f /opt/puppetlabs/puppet/cache/client_data/catalog/<CERTNAME>.json
   ~~~

5. Run the following commands:

   ~~~
   chown pe-puppet:pe-puppet /etc/puppetlabs/puppet/puppet.conf   
   chown -R pe-puppet:pe-puppet /etc/puppetlabs/puppet/ssl/
   chown -R pe-console-services /opt/puppetlabs/server/data/console-services/certs/
   chown -R pe-postgres:pe-postgres /opt/puppetlabs/server/data/postgresql/9.4/data/certs/
   chown -R pe-puppetdb:pe-puppetdb /etc/puppetlabs/puppetdb/ssl/

   ~~~

6. Restart PE services. Run the following commands:

   a. On the Puppet master:

   ~~~
   puppet resource service pe-puppetserver ensure=running
   puppet resource service pe-orchestration-services ensure=running
   puppet resource service pe-nginx ensure=running
   ~~~

   b. On a monolithic install, run the following commands on the Puppet master; for a split install, run them on the node assigned the PuppetDB/PostgreSQL component:

   ~~~
  puppet resource service pe-postgresql ensure=stopped
  puppet resource service pe-postgresql ensure=running
  puppet resource service pe-puppetdb ensure=running
   ~~~

   c. On a monolithic install, run the following on the Puppet master; for a split install, run it on the node assigned the PE console component:

   ~~~
   puppet resource service pe-console-services ensure=running
   ~~~

    d.  On a monolithic install, run the following on the Puppet master; for a split install, run it on the Puppet master, PuppetDB, and PE console components:

   ~~~
   puppet resource service puppet ensure=running
   ~~~

8. Restore modules, manifests, hieradata, and, if necessary, Code Manager SSH keys.

   These are typically located in the `/etc/puppetlabs/` directory, but you may have configured them in another location.
