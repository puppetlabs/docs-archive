# Configuring and tuning PuppetDB

After you've installed Puppet Enterprise, optimize it for your environment by configuring and tuning PuppetDB configuration as needed.

This page covers a few key topics, but additional settings and information about configuring PuppetDB is available in the [PuppetDB configuration documentation](https://docs.puppet.com/puppetdb/latest/configure.html). Be sure to check that the PuppetDB docs version you're looking at matches the one version of PuppetDB in your PE.

**Related information**  


[Methods for configuring Puppet Enterprise](config_intro.md#)

[Tuning monolithic installations](tuning_monolithic.md#)

[Configure settings with Hiera](config_intro.md#)

[Configure ulimit for PE services](config_ulimit.md#)

[Increase the Java heap size for PE Java services](config_java_args.md#)

## Configure agent run reports

By default, every time Puppet runs, the master generates agent run reports and submits them to PuppetDB. You can enable or disable this as needed.

### About this task

To enable or disable agent run reports using the console:

### Procedure

1.  Click **Classification**, and in the **PE Infrastructure** group, select the **PE Master** group.

2.  On the **Configuration** tab, add the `puppet_enterprise::profile::master::puppetdb` class, select the `report_processor_ensure` parameter, and enter the value `present` to enable agent run reports or `absent` to disable agent run reports.

3.  Click **Add parameter** and commit changes.

4.  On the nodes hosting the master and console, run Puppet.


## Configure command processing threads

The `command_processing_threads` setting defines how many command processing threads PuppetDB uses to sort incoming data. Each thread can process a single command at a time. The setting defaults to half the number of cores in your system.

### About this task

To set the number of threads using `pe.conf`:

### Procedure

1.  Add the following code to your `pe.conf` file on your master and set your new parameter.

    ```
    puppet_enterprise::puppetdb::command_processing_threads: <number of threads>
    ```

2.  Run `puppet agent -t`


## Configure how long before PE stops managing deactivated nodes

Use the `node-purge-ttl` parameter to set the "length of time" value before PE automatically removes nodes that have been deactivated or expired. This also removes all facts, catalogs, and reports for the relevant nodes.

### About this task

To change the amount of time before nodes are purged using the console:

### Procedure

1.  Click **Classification**, and in the PE Infrastructure group, select the PE Database group.

2.  On the **Configuration** tab, find the `puppet_enterprise::profile::puppetdb` class, find the `node_purge_ttl` parameter, and change its value to the desired amount of time.

    To change the unit of time, use the following suffixes:

    -   `d` - days

    -   `h` - hours

    -   `m` - minutes

    -   `s` - seconds

    -   `ms` - milliseconds

        For example, to set the purge time to 14 days:

    ```
    puppet_enterprise::profile::puppetdb::node_purge_ttl: '14d'
    ```

3.  Click **Add parameter** and commit changes.

4.  On the nodes hosting the master and console, run Puppet.


## Change the PuppetDB user password

The console uses a database user account to access its PostgreSQL database. Change it if it is compromised or to comply with security guidelines.

### About this task

To change the password:

### Procedure

1.  Stop the `pe-puppetdb``puppet` service by running `puppet resource service pe-puppetdb ensure=stopped`

2.  On the database server \(which might or might not be the same as PuppetDB, depending on your deployment's architecture\), use the PostgreSQL administration tool of your choice to change the user's password. With the standard PostgreSQL client, you can do this by running `ALTER USER console PASSWORD '<new password>';`

3.  Edit `/etc/puppetlabs/puppetdb/conf.d/database.ini` on the PuppetDB server and change the `password:` line under `common` or `production`**,** depending on your configuration, to contain the new password.

4.  Start the `pe-puppetdb` service on the console server by running `puppet resource service pe-puppetdb ensure=running`


## Configure excluded facts

Use the `facts_blacklist` parameter exclude facts from being stored in the PuppetDB database.

### About this task

To specify which facts you want to exclude using Hiera:

### Procedure

1.  Add the following to you default `.yaml` file and list the facts you want to exclude. For example, to exclude the facts `system_uptime_example` and `mountpoints_example`:

2.  ```
puppet_enterprise::puppetdb::database_ini::facts_blacklist:
- 'system_uptime_example'
- 'mountpoints_example'
```

3.  To compile changes, run `puppet agent -t`


