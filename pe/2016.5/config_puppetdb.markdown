---
layout: default
title: "Configuring and tuning PuppetDB"
canonical: "/pe/latest/config_PuppetDB.html"
---

We recommend tuning the following settings as needed to improve the performance of PuppetDB.

See [Tuning monolithic installations](./config_monolithic.html) for recommended settings based on various monolithic installation sizes.

## Java args

You can increase the JVM (Java Virtual Machine) memory that is allocated to Java services running on PuppetDB. This memory allocation is known as the Java heap size.

Instructions for using the PE console to increase the Jave heap size are detailed on on the [Configuring Java Arguments for PE](./config_java_args.html#pe-console-service) page.

## Increasing the ulimit for pe-puppetdb

The various services in PE require up to one file handle per connected client. On most OS configurations, the defaults will not be high enough for more than a couple hundred clients. To support more clients, you need to increate the number of allowed file handles.

Instructions for increasing the number of file handles are detailed on the [Increasing the ulimit for PE services](./config_ulimit.html) page.

## Configuring command processing threads

This defines how many command processing threads PuppetDB uses to sort incoming data. Each thread can process a single command at a time. This setting defaults to half the number of cores in your system.

To set the number of command processors to use, [edit your Hiera default `.yaml` file](./config_intro.html#configure-settings-with-hiera), and set the `command_processing_threads` setting using the following code:

~~~
puppet_enterprise::puppetdb::command_processing_threads: <NUMBER of THREADS>
~~~

## Configuring broker memory

Broker memory refers to the [`memory-usage` parameter]({{puppetdb}}/configure.html#memory-usage).

> **Note:** Tuning this setting involves writing Puppet code, as documented at [Playing nice with the PuppetDB module]({{puppetdb}}/configure.html#playing-nice-with-the-puppetdb-module).

## Configuring report processors

The Puppet master generates agent run reports every time Puppet runs and submits these to PuppetDB. These can be enabled or disabled from the PE console or hiera. By default, this setting is set to `present` (enabled).

**To enable/disable run report processors using the console**:

1. In the console, click **Nodes** > **Classification**, and in the **PE Infrastructure** group, select the **PE Master** group.
2. On the **Classes** tab, locate or add the `puppet_enterprise::profile::master::puppetdb` class, and specify parameters:

   - **Parameter** -- Select **report_processor_ensure**.

   - **Value** -- Enter `absent` to disable or `present` to enable.

3. Click **Add parameter**, and commit changes.

**To enable/disable run report processors using Hiera**:

To enable/disable run report processors, you need to [edit your Hiera default `.yaml` file](./config_intro.html#configure-settings-with-hiera), and set the `report_processor_ensure` setting using the following code:

	puppet_enterprise::profile::master::puppetdb::report_processor_ensure: <PRESENT or ABSENT>

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
   puppet_enterprise::profile::puppetdb::node_purge_ttl: 14d
   ~~~
