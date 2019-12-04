# Configuring and tuning PuppetDB

After you've installed Puppet Enterprise, optimize it for your environment by configuring and tuning PuppetDB configuration as needed.

Additional information about configuring PuppetDB is available in the [PuppetDB configuration documentation](https://docs.puppet.com/puppetdb/latest/configure.html). Be sure to check that the PuppetDB docs version you're looking at matches the one version of PuppetDB in your PE.

**Related information**  


[Tuning monolithic installations](tuning_monolithic.md#)

[Configure settings with Hiera](config_intro.md#)

[Configure ulimit for PE services](config_ulimit.md#)

[Increase the Java heap size for PE Java services](config_java_args.md#)

## Configure agent run reports in the console

By default, every time Puppet runs, the master generates agent run reports and submits them to PuppetDB. These agent run reports can be enabled or disabled in the console.

### Procedure

1.  Click **Classification**, and in the **PE Infrastructure** group, select the **PE Master** group.

2.  On the **Configuration** tab, locate or add the `puppet_enterprise::profile::master::puppetdb` class, select the `report_processor_ensure` parameter, and enter the value `present` to enable agent run reports or `absent` to disable agent run reports.

3.  Click **Add parameter**, and commit changes.


## Configure agent run reports in Hiera

By default, every time Puppet runs, the master generates agent run reports and submits them to PuppetDB. These agent run reports can be enabled or disabled in Hiera.

### Procedure

1.  Edit your Hiera default `.yaml` file.

2.  Set the `report_processor_ensure` setting to `present` \(enabled\) or `absent` \(disabled\), as shown in the following code:

    ```
    puppet_enterprise::profile::master::puppetdb::report_processor_ensure: <PRESENT or ABSENT>
    ```


## Configure command processing threads

The `command_processing_threads` setting defines how many command processing threads PuppetDB uses to sort incoming data. Each thread can process a single command at a time.

### About this task

This setting defaults to half the number of cores in your system.

### Procedure

1.  Set the number of command processing threads by editing your Hiera default `.yaml` file to add the following code:

    ```
    puppet_enterprise::puppetdb::command_processing_threads: <NUMBER OF THREADS>
    ```


### Results

## Configure node-purge-ttl

Use this parameter to set the "time-to-live" value before PE automatically deletes nodes that have been deactivated or expired. This also deletes all facts, catalogs, and reports for the relevant nodes.

### Procedure

1.  Edit the Hiera default `.yaml` file and set the `node_purge_ttl` setting.

    For example, to specify a value of 14 days:

    ```
    puppet_enterprise::profile::puppetdb::node_purge_ttl: '14d'
    ```

    To set time using other units, use the following suffixes:

    -   `d` - days

    -   `h` - hours

    -   `m` - minutes

    -   `s` - seconds

    -   `ms` - milliseconds


## Change the PuppetDB user password

The console uses a database user account to access its PostgreSQL database. Change it if it is compromised or to comply with security guidelines.

### About this task

To change the password:

### Procedure

1.  Stop the `pe-puppetdb``puppet` service by running `puppet resource service pe-puppetdb ensure=stopped`

2.  On the database server \(which might or might not be the same as PuppetDB, depending on your deployment's architecture\) use the PostgreSQL administration tool of your choice to change the user's password. With the standard psql client, you can do this by running `ALTER USER console PASSWORD '<new password>';`

3.  Edit `/etc/puppetlabs/puppetdb/conf.d/database.ini` on the PuppetDB server and change the `password:` line under `common` or `production`**,** depending on your configuration, to contain the new password.

4.  Start the `pe-puppetdb` service on the console server by running `puppet resource service pe-puppetdb ensure=running`


## Configure blacklisted facts

Use the `facts_blacklist` to exclude facts from being stored in the PuppetDB database.

### Procedure

1.  Edit the Hiera default `.yaml` file and set the `facts_blacklist` setting.

    For example, to prevent the `system_uptime` and `mountpoints` facts from being stored in PuppetDB:

    ```
    puppet_enterprise::puppetdb::database_ini::facts_blacklist:
    - 'system_uptime' 
    - 'mountpoints' 
    ```


