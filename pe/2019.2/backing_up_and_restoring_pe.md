---
author: Jean Bond <jean@puppet.com\>
---

# Backing up and restoring Puppet Enterprise

Keep regular backups of your PE infrastructure. Backups allow you to more easily migrate to a new master, troubleshoot, and quickly recover in the case of system failures.

**Note:** These instructions are for standard \(previously called monolithic\) installations only. Split installations, where the master, console, and PuppetDB are installed on separate nodes, are no longer supported in PE. We recommend migrating from an existing split installation to a standard installation—with or without compilers—and a standalone PE-PostgreSQL node. See the docs for how to [Migrate from a split to a standard installation](upgrading_pe.md#).

**Important:** Always store your backups in a safe location that you can access if your master fails. Backup files are not encrypted, so secure these as you would any sensitive information.

You can use the `puppet-backup` command to back up and restore your master. You can't use this command to back up compilers. By default, the backup command creates a backup of:

-   Your PE configuration, including license, classification, and RBAC settings. However, configuration backup does not include Puppet gems or Puppet Server gems.

-   PE CA certificates and the full SSL directory.

-   The Puppet code deployed to your code directory at backup time.

-   PuppetDB data, including facts, catalogs and historical reports.


Each time you create a new backup, PE creates a single, timestamped backup file, named in the format `pe_backup-<TIMESTAMP>.tgz`. This file includes everything you're backing up. By default, PE writes backup files to `/var/puppetlabs/backups`, but you can change this location when you run the backup command. When you restore, specify the backup file you want to restore from.

If you are restoring to a previously existing master, uninstall and reinstall PE before restoring your infrastructure. If you are restoring or migrating your infrastructure to a master with a different hostname than the previous master, you'll redirect your agents to the new master during the restore process. In both cases, the freshly installed PE must be the same PE version that was in use when you backed up the files.

By default, backup and restore functions include your Puppet configuration, certificates, code, and PuppetDB. However, you can limit the scope of backup and restore with command line options. This allows you to back up to or restore from multiple files. This is useful if you want to back up some parts of your infrastructure more often than others.

For example, if you have frequent code changes, you might back up the code more often than you back up the rest of your infrastructure. When you limit backup scope, the backup file contains only the specified parts of your infrastructure. Be sure to give your backup file a name that identifies the scope so that you always know what a given file contains.

During restore, you must restore all scopes: code, configuration, certificates, and PuppetDB. However, you can restore each scope from different files, either by restoring from backup files with limited scope or by limiting the scope of the restore. For example, by specifying scope when you run the restore command, you could restore code, configuration, and certificates from one backup file and PuppetDB from a different one.

## Back up your infrastructure

PE backup creates a copy of your Puppet infrastructure, including configuration, certificates, code, and PuppetDB.

### Before you begin

You can back up only your master. The backup and restore commands are not supported for compilers.

### Procedure

1.  On your master, from the command line logged in as root, run `puppet-backup create`

    To change the default command behavior, pass option flags and values to the command. See the backup and restore [reference](backing_up_and_restoring_pe.md#) for a complete list of options.

    For example, to limit a backup to certain parts of your PE infrastructure, pass the `--scope` option, specifying the scopes in a comma-separated list. To change the name of your backup file, pass the `--name` option with a string specifying the filename. For example, to back up PuppetDB only and name your file with the scope and date, run:

    ```
    puppet-backup create --scope=puppetdb --name=puppetdb_backup_03032018.tgz
    
    ```

2.  Make a backup of the secret key used to encrypt and decrypt sensitive data stored in the inventory service.

    Secure the key as you would any sensitive information. The secret key is stored at: `/etc/puppetlabs/orchestration-services/conf.d/secrets/keys.json`

    CAUTION:

    The backup command does not include the secret key. You must back up this information separately.


### Results

By default, if you don't specify the `--dir` or `--name` options, PE creates files to `/var/puppetlabs/backups` and names them with a timestamp, such as `pe_backup-<TIMESTAMP>.tgz`

### What to do next

After backing up, you can move your backup files to another location. Always store your backups in a safe location that is not on the master.

**Related information**  


[Configuration parameters and the pe.conf file](installing_pe.md#)

## Restore your infrastructure

Use the restore commands to migrate your PE master to a new host or to recover from system failure.

### Before you begin

Remember that you must restore files to a fresh installation of the same version of PE used in your backup file.

### Procedure

1.  If you are restoring to an existing master, purge any existing PE installation from it.

    1.  On the master, uninstall PE by running `sudo /opt/puppetlabs/bin/puppet-enterprise-uninstaller -p -d`

    2.  Ensure that the directories `/opt/puppetlabs/` and `/etc/puppetlabs/` are no longer present on the system.

    For details about uninstalling PE, see the [uninstalling](uninstalling.md#) documentation.

2.  Install PE on the master you are restoring to. This must be the same PE version that was used for the backup files.

    1.  If you don't have the PE installer script on the machine you want to restore to, download the installer tarball to the machine from the [download](https://puppet.com/download-puppet-enterprise) site, and unpack it by running `tar -xf <TARBALL_FILENAME>`

    2.  Navigate to the directory containing the install script. The installer script is located in the PE directory created when you unpacked the tarball.

    3.  Install PE by running `sudo ./puppet-enterprise-installer`

    For details about the PE installation process, see the [installing](installing_pe.md#) documentation.

3.  On the master logged in as root, restore your PE infrastructure by running `puppet-backup restore <BACKUP-FILENAME>`

    To change the default command behavior, pass option flags and values to the command. See the command [reference](backing_up_and_restoring_pe.md#) for a complete list of options.

    For example, to restore your scope, certificates, and code from one backup file, but your PuppetDB data from another, pass the `--scope` option:

    ```
    puppet-backup restore pe_backup-2018-03-03_20.07.15_UTC.tgz --scope=config,certs,code
    
    puppet-backup restore pe_backup-2018-04-04_20.07.15_UTC.tgz --scope=puppetdb
    ```

4.  Restore your backup of the secret key used to encrypt and decrypt sensitive data stored in the inventory service.

    The secret key is stored at: `/etc/puppetlabs/orchestration-services/conf.d/secrets/keys.json`

    CAUTION:

    The restore command does not include the secret key. You must restore this information separately.

5.  Ensure the secret key ownership is `pe-orchestration-services:pe-orchestration-services`.

6.  Restart the pe-orchestration-services service.

7.  If you restored PE onto a master with a different hostname than the original installation, and you have not configured the `dns_alt_names` setting in the `pe.conf` file, redirect your agents to the new master. An easy way to do this is by running a task with the Bolt task runner.

    1.  Download and [install](https://puppet.com/docs/bolt/0.x/bolt_installing.html) Bolt , if you don't already have it installed.

    2.  Update the `puppet.conf` file to point all agents at the new master by running:

        ```
        bolt task run puppet_conf action=set section=agent setting=server value=<RESTORE_HOSTNAME> --nodes <COMMA-SEPARATED LIST OF NODES>           
        ```

    3.  Run the agent on all nodes by running: 

        ```
        bolt command run "puppet agent -t" --nodes <COMMA-SEPARATED LIST OF NODES>
        ```

8.  If your infrastructure had Code Manager enabled when your backup file was created, deploy your code by running:

    ```
    puppet access login
    puppet code deploy --all --wait       
    ```

9.  Test the agents' connection to the master by running `puppet agent --test`


### Results

**Related information**  


[Configuration parameters and the pe.conf file](installing_pe.md#)

[Installing Puppet Enterprise](installing_pe.md#)

[Uninstalling](uninstalling.md#)

## Backup and restore reference

Use these options to change the backup and restore scope and other options for the `puppet-backup` command. 

**Note:** Backup commands must be run as root.

### puppet-backup create

Run the `puppet-backup create` command to create backup files of your PE infrastructure.

Usage:

```
puppet-backup create [--dir=<DIRECTORY_PATH>] [--name=<BACKUP_NAME>.tgz] [--scope=<SCOPE_LIST>] [--force]
```

|Option|Description|Values|Default|
|------|-----------|------|-------|
|`--dir=BACKUP_DIR`|Specifies the directory to write the backup file to.|A valid filepath that the pe-postgres user has write permission for.|`/var/puppetlabs/backups/`|
|`--name=BACKUP_NAME.tgz`|Specifies the name for the backup file.|A string designating a file name.|`pe_backup-<TIMESTAMP>.tgz`|
|`--pe-environment=ENVIRONMENT`|Specifies the environment to back up. To ensure configuration is recovered correctly, this must be the environment where your master is located.|A valid environment name.|`production`|
|`--scope=SCOPE`|Scope of backup to create.|Either `all` or any combination of the other available scopes, as a comma-separated list: -   `config`: PE configuration including license, classification, and RBAC settings. Does not include Puppet gems or Puppet Server gems.

-   `certs`: PE CA certificates and full SSL directory

-   `code`: Puppet code deployed to your codedir at backup time.

-   `puppetdb`: PuppetDB data, including facts, catalogs, and historical reports

-   `all`: All listed scopes.


|`all`|
|`--force`|Bypass validation checks and ignore warnings.|None.|If you don't specify `--force`, PE verifies that the destination directory exists and has enough space for the backup process.|

For example, to create a backup of PuppetDB only and give it a name that shows the scope of the file:

```
puppet-backup create --scope=puppetdb --name=puppetdb_backup_03032018.tgz
```

### puppet-backup restore

Run the `puppet-backup restore` command to restore your PE infrastructure from backup files.

Usage:

```
puppet-backup restore <PATH/TO/BACKUP_FILE.tgz> [--scope=<SCOPE_LIST>] [--force]
```

|Option|Description|Values|Default|
|------|-----------|------|-------|
|`--pe-environment=ENVIRONMENT`|Specifies the environment to restore.|A valid environment name for which you have an existing backup.|`production`|
|`--scope=SCOPE`|Scope of backup to restore. All scopes must eventually be restored, but you can restore different scopes from different backup files with successive restore commands.|Either `all` or any combination of the other available scopes, as a comma-separated list: -   `config`: PE configuration including license, classification, and RBAC settings. Does not include Puppet gems or Puppet Server gems.

-   `certs`: PE CA certificates and full SSL directory

-   `code`: Puppet code deployed to your codedir at backup time.

-   `puppetdb`: PuppetDB data, including facts, catalogs, and historical reports

-   `all`: All listed scopes.


|`all`|
|`--force`|Bypass validation checks and ignore warnings.|None.|If you don't specify `--force`, PE verifies that the destination directory exists and has enough space for the restore process. Returns warnings for insufficient space or invalid locations.|

For example, to restore PuppetDB from one backup file and restore configuration, certificates, and code from another backup file:

```
puppet-backup restore /mybackups/pe_backup_03032018.tgz --scope=puppetdb

puppet-backup restore /mybackups/pe_backup_04042018.tgz --scope=config,certs,code
```

### Directories and data backed up

|Scope|Directories and databases backed up|
|-----|-----------------------------------|
|`certs`|-   `/etc/puppetlabs/puppet/ssl/`


|
|`code`|-   `/etc/puppetlabs/code/`

-   `/etc/puppetlabs/code-staging/`

-   `/opt/puppetlabs/server/data/puppetserver/filesync/storage/`


|
|`config`|-   Orchestrator database

-   RBAC database

-   Classifier database

-   `/etc/puppetlabs/`, except:

    -   `/etc/puppetlabs/code/`

    -   `/etc/puppetlabs/code-staging/`

    -   `/etc/puppetlabs/puppet/ssl`

-   `/opt/puppetlabs/` , except:

    -   `/opt/puppetlabs/puppet`

    -   `/opt/puppetlabs/server/pe_build`

    -   `/opt/puppetlabs/server/data/packages`

    -   `/opt/puppetlabs/server/apps`

    -   `/opt/puppetlabs/server/data/postgresql`

    -   `/opt/puppetlabs/server/data/enterprise/modules`

    -   `/opt/puppetlabs/server/data/puppetserver/vendored-jruby-gems`

    -   `/opt/puppetlabs/bin`

    -   `/opt/puppetlabs/client-tools`

    -   `/opt/puppetlabs/server/share`

    -   `/opt/puppetlabs/server/data/puppetserver/filesync/storage`

    -   `/opt/puppetlabs/server/data/puppetserver/filesync/client`


|
|`puppetdb`|-   PuppetDB

-   `/opt/puppetlabs/server/data/puppetdb`


|

