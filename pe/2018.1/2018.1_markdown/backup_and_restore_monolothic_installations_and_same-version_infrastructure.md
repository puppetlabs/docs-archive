---
author: Logan Mantyla <logan.mantyla@puppet.com\>
---

# Backup and restore monolithic installations and same-version infrastructure

Use this backup and restore method only if you are on a monolithic install and are not upgrading your version of PE.

## Back up your PE infrastructure

PE backup creates a copy of your Puppet infrastructure, including configuration, certificates, code, and PuppetDB.

### Before you begin

You can back up monolithic masters only, whether they are the only master or master of masters. The backup and restore commands are not supported for split installations or compile masters.

### Procedure

1.  On your primary master, from the command line logged in as root, run `puppet-backup create`

    To change the default command behavior, pass option flags and values to the command. See the backup and restore [reference](backup_and_restore_monolothic_installations_and_same-version_infrastructure.md#) for a complete list of options.

    For example, to limit a backup to certain parts of your PE infrastructure, pass the `--scope` option, specifying the scopes in a comma-separated list. To change the name of your backup file, pass the `--name` option with a string specifying the filename. For example, to back up PuppetDB only and name your file with the scope and date, run:

    ```
    puppet-backup create --scope=puppetdb --name=puppetdb_backup_03032018.tgz
    
    ```


### Results

By default, if you don't specify the `--dir` or `--name` options, PE creates files to `/var/puppetlabs/backups` and names them with a timestamp, such as `pe_backup-<TIMESTAMP>.tgz`

### What to do next

After backing up, you can move your backup files to another location. Always store your backups in a safe location that is not on the master.

**Related information**  


[Configuration parameters and the pe.conf file](installing_pe.md#)

## Restore your Puppet Enterprise infrastructure

Use the restore commands to migrate your PE master to a new host or to recover from system failure.

### Before you begin

Remember that you must restore files to a fresh installation of the same version of PE used in your backup file.

### Procedure

1.  If you are restoring to an existing master, purge any existing PE installation from it.

    1.  On the master, uninstall PE by running `sudo /opt/puppetlabs/bin/puppet-enterprise-uninstaller -p -d`

    2.  Ensure that the directories `/opt/puppetlabs/` and `/etc/puppetlabs/` are no longer present on the system.

    For details about uninstalling PE, see the [uninstalling documentation](https://puppet.com/docs/pe/latest/uninstalling.html).

2.  Install PE on the master you are restoring to. This must be the same PE version that was used for the backup files.

    1.  If you don't have the PE installer script on the machine you want to restore to, download the installer tarball to the machine from the [download](https://puppet.com/download-puppet-enterprise) site, and unpack it by running `tar -xf <TARBALL_FILENAME>`

    2.  Navigate to the directory containing the install script. The installer script is located in the PE directory created when you unpacked the tarball.

    3.  Install PE by running `sudo ./puppet-enterprise-installer`

    For details about the PE installation process, see the [installing documentation](https://puppet.com/docs/pe/latest/installing.html).

3.  On the master logged in as root, restore your PE infrastructure by running `puppet-backup restore <BACKUP-FILENAME>`

    To change the default command behavior, pass option flags and values to the command. See the command [reference](https://puppet.com/docs/pe/latest/backing_up_and_restoring_pe.html#backup-and-restore-reference) for a complete list of options.

    For example, to restore your scope, certificates, and code from one backup file, but your PuppetDB data from another, pass the `--scope` option:

    ```
    puppet-backup restore pe_backup-2018-03-03_20.07.15_UTC.tgz --scope=config,certs,code
    
    puppet-backup restore pe_backup-2018-04-04_20.07.15_UTC.tgz --scope=puppetdb
    ```

4.  If you restored PE onto a master with a different hostname than the original installation, and you have not configured the `dns_alt_names` setting in the `pe.conf` file, redirect your agents to the new master. An easy way to do this is by running a task with the Bolt task runner.

    1.  Download and [install](https://puppet.com/docs/bolt/0.x/bolt_installing.html) Bolt , if you don't already have it installed.

    2.  Update the `puppet.conf` file to point all agents at the new master by running:

        ```
        bolt task run puppet_conf action=set section=agent setting=server value=<RESTORE_HOSTNAME> --nodes <COMMA-SEPARATED LIST OF NODES>
        bolt task run puppet_conf action=set section=main setting=server value=<RESTORE_HOSTNAME> --nodes <COMMA-SEPARATED LIST OF NODES>
        ```

    3.  Run `puppet agent -t --no-use_cached_catalog` on the newly restored master **twice** to apply changes and then restart services.

    4.  Run `puppet agent -t --no-use_cached_catalog` on all agent nodes to test connection to the new master.

5.  If your infrastructure had Code Manager enabled when your backup file was created, deploy your code by running:

    ```
    puppet access login
    puppet code deploy --all --wait       
    ```


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
|`--pe-environment=ENVIRONMENT`|Specifies the environment to back up. To ensure configuration is recovered correctly, this should be the environment where your master is located.|A valid environment name.|`production`|
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

