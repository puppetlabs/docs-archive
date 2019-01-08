---
layout: default
title: "PE 2015.2 » Configuration"
subtitle: "Configuring & Tuning PuppetDB"
canonical: "/pe/latest/config_PuppetDB.html"
---

We recommend tuning the following settings as needed to improve the performance of PuppetDB.

### Java Args

You can increase the JVM (Java Virtual Machine) memory that is allocated to Java services running on PuppetDB. This memory allocation is known as the Java heap size.

Instructions for using the PE console to increase the Jave heap size are detailed on on the [Configuring Java Arguments for PE](./config_java_args.html#pe-console-service) page.

### Configuring Report Processors

The Puppet master generates agent run reports every time Puppet runs and submits these to PuppetDB. These can be enabled or disabled from the PE console or hiera. By default, this setting is set to `present` (enabled).

**To enable/disable run report processors using the console**:

1. In the PE console, navigate to the **Classification** page (**Nodes** > **Classification**).
2. Click the **PE Master** group.
3. In the **PE Master** group page, click the **Classes** tab.
4. Locate (or add) the `puppet_enterprise::profile::master::puppetdb` class, and from the **Parameter** drop-down list, select `report_processor_ensure`.
5. In the Value field, enter `absent` (to disable) or `present` (to enable).
6. Click **Add parameter**, and then click the **Commit change** button.

**To enable/disable run report processors using Hiera**:

To enable/disable run report processors, you need to [edit your Hiera default `.yaml` file](./config_intro.html#configure-settings-with-hiera), and set the `report_processor_ensure` setting using the following code:

	puppet_enterprise::profile::master::puppetdb::report_processor_ensure: <PRESENT or ABSENT>
	
### Configure `node-purge-ttl`

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
	