---
author: Jean Bond <jean@puppet.com\>
---

# Backing up and restoring Puppet Enterprise

Keep regular backups of your PE infrastructure. Backups allow you to more easily migrate to a new master, troubleshoot, and quickly recover in the case of system failures.

**Note:** Split installations, where the master, console, and PuppetDB are installed on separate nodes, are deprecated in later versions of PE. We recommend migrating from an existing split installation to a monolithic installation—with or without compilers—and a standalone PE-PostgreSQL node. See the docs for how to [Migrate from a split to a monolithic installation](upgrading_pe.md#)

**Important:** Always store your backups in a safe location that you can access if your master fails. Backup files are not encrypted, so secure these as you would any sensitive information.

You can use the `puppet-backup` command to back up and restore your primary master or master of masters in monolithic installations only. You can't use this command to back up split installations or compile masters. By default, the backup command creates a backup of:

-   Your PE configuration, including license, classification, and RBAC settings. However, configuration backup does not include Puppet gems or Puppet Server gems.

-   PE CA certificates and the full SSL directory.

-   The Puppet code deployed to your code directory at backup time.

-   PuppetDB data, including facts, catalogs and historical reports.


Each time you create a new backup, PE creates a single, timestamped backup file, named in the format `pe_backup-<TIMESTAMP>.tgz`. This file includes everything you're backing up. By default, PE writes backup files to `/var/puppetlabs/backups`, but you can change this location when you run the backup command. When you restore, specify the backup file you want to restore from.

If you are restoring to a previously existing master, uninstall and reinstall PE before restoring your infrastructure. If you are restoring or migrating your infrastructure to a master with a different hostname than the previous master, you'll redirect your agents to the new master during the restore process. In both cases, the freshly installed PE must be the same PE version that was in use when you backed up the files.

By default, backup and restore functions include your Puppet configuration, certificates, code, and PuppetDB. However, you can limit the scope of backup and restore with command line options. This allows you to back up to or restore from multiple files. This is useful if you want to back up some parts of your infrastructure more often than others.

For example, if you have frequent code changes, you might back up the code more often than you back up the rest of your infrastructure. When you limit backup scope, the backup file contains only the specified parts of your infrastructure. Be sure to give your backup file a name that identifies the scope so that you always know what a given file contains.

During restore, you must restore all scopes: code, configuration, certificates, and PuppetDB. However, you can restore each scope from different files, either by restoring from backup files with limited scope or by limiting the scope of the restore. For example, by specifying scope when you run the restore command, you could restore code, configuration, and certificates from one backup file and PuppetDB from a different one.

-   **[Backup and restore split installations and upgrades](backup_and_restore_split_installations_and_upgrades.md#)**  

-   **[Backup and restore monolithic installations and same-version infrastructure](backup_and_restore_monolothic_installations_and_same-version_infrastructure.md#)**  


