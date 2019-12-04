---
layout: default
title: "Configuring and tuning PuppetDB"
canonical: "/pe/latest/config_PuppetDB.html"
---

After you've installed Puppet Enterprise (PE), optimize it for your environment by configuring and tuning PuppetDB configuration as needed.

See [Tuning monolithic installations](./config_monolithic.html) for recommended settings based on various monolithic installation sizes.

## Configure agent run reports in the console

By default, every time Puppet runs, the Puppet master generates agent run reports and submits them to PuppetDB. These agent run reports can be enabled or disabled in the PE console.

1. Click **Nodes** > **Classification**, and in the **PE Infrastructure** group, select the **PE Master** group.

2. On the **Classes** tab, locate or add the `puppet_enterprise::profile::master::puppetdb` class, select the `report_processor_ensure` parameter, and enter the value `present` to enable agent run reports or `absent` to disable agent run reports.

3. Click **Add parameter**, and commit changes.

## Configure agent run reports in Hiera

By default, every time Puppet runs, the Puppet master generates agent run reports and submits them to PuppetDB. These agent run reports can be enabled or disabled in Hiera.

1. [Edit your Hiera default `.yaml` file](./config_intro.html#configure-settings-with-hiera), and set the `report_processor_ensure` setting to `present` (enabled) or `absent` (disabled), as shown in the following code:

~~~
    puppet_enterprise::profile::master::puppetdb::report_processor_ensure: <PRESENT or ABSENT>
~~~

## Configure command processing threads

The `command_processing_threads` setting defines how many command processing threads PuppetDB uses to sort incoming data. Each thread can process a single command at a time. This setting defaults to half the number of cores in your system.

1. Set the number of `command_processing_threads` by [editing your Hiera default `.yaml` file](./config_intro.html#configure-settings-with-hiera) using the following code:

~~~
    puppet_enterprise::puppetdb::command_processing_threads: <NUMBER of THREADS>
~~~

## Configure broker memory

The [`memory-usage` parameter]({{puppetdb}}/configure.html#memory-usage) sets the maximum amount of memory in megabytes available for PuppetDB's ActiveMQ Broker.

> **Note:** Tuning this setting involves writing Puppet code, as documented at [Playing nice with the PuppetDB module]({{puppetdb}}/configure.html#playing-nice-with-the-puppetdb-module).

## Configure `node-purge-ttl`

Use this parameter to set the "time-to-live" value before PE automatically deletes nodes that have been deactivated or expired. This will also delete all facts, catalogs, and reports for the relevant nodes. 

Specify the time as a string using any of the following suffixes:

- `d`  - days
- `h`  - hours
- `m`  - minutes
- `s`  - seconds
- `ms` - milliseconds

For example, a value of `14d` would set the time-to-live to 14 days.

1. [Edit your Hiera default `.yaml` file](./config_intro.html#configure-settings-with-hiera), and set the `node_purge_ttl` setting, as shown in the following code:

   ~~~
   puppet_enterprise::profile::puppetdb::node_purge_ttl: '14d'
   ~~~

## Changing the PuppetDB user/password

The console uses a database user account to access its PostgreSQL database. If this user's password is compromised, or if it needs to be changed periodically, do the following:

1. Stop the `pe-puppetdb` service with `puppet resource service pe-puppetdb ensure=stopped`.
2. On the database server (which may or may not be the same as PuppetDB, depending on your deployment's architecture) use the PostgreSQL administration tool of your choice to change the user's password. With the standard `psql` client, you can do this with `ALTER USER console PASSWORD '<new password>';`.
3. Edit `/etc/puppetlabs/puppetdb/conf.d/database.ini` on the PuppetDB server and change the `password:` line under __common__ (or under __production,__ depending on your configuration) to contain the new password.
4. Start the `pe-puppetdb` service on the console server with with `puppet resource service pe-puppetdb ensure=running`.

*******

**Related links**

- Increase the ulimit for pe-puppetdb

    Services in PE require up to one file handle per connected client. On most OS configurations, the defaults are not high enough for more than a couple of hundred clients. Support more clients by increasing the number of allowed file handles.

    View instructions to [increase the ulimit for PE services](./config_ulimit.html).

- Tune the Java heap size for pe-puppetdb

   Java heap size is the JVM (Java Virtual Machine) memory allocated to Java services running on PuppetDB.

    Instructions to change the Java heap size using the PE console are on the [Configuring Java Arguments for PE](./config_java_args.html#pe-console-service) page.

- Additional PuppetDB configuration information

  Additional PuppetDB configuration information is available in the [PuppetDB documentation]({{puppetdb}}/configure.html). When consulting this documentation, be sure to check the PuppetDB version against the one included in your version of PE.