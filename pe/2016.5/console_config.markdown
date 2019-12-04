---
layout: default
title: "Configuring and tuning the console"
canonical: "/pe/latest/console_config.html"
---

## Changing session duration

There are a few options that can be configured for access control:

- `password-reset-expiration`  When a user doesn't remember their current password, an administrator can generate a token for them to change their password. The duration, in hours, that this generated token is valid can be changed with this config parameter. The default value is 24.
- `failed-attempts-lockout`  This parameter is a positive integer that specifies how many failed login attempts are allowed on an account before that account is revoked. The default value is 10.
- `rbac_session_timeout`  This parameter is a positive integer that specifies how long a user's session should last, in minutes. This session is the same across node classification, RBAC, and the console. The default value is 60.

To change the defaults for `password-reset-expiration` or `failed-attempts-lockout` , add a new file to the `/etc/puppetlabs/console-services/conf.d` directory. The file name is arbitrary, but the file format is [HOCON](./config_hocon.html).

    rbac: {
     password-reset-expiration: 24
     failed-attempts-lockout: 10
    }

Then reload pe-console-services (`sudo service pe-console-services reload`).

You can change this value of `rbac_session_timeout` in the PE console.

1. In the console, click **Nodes** > **Classification**, and in the **PE Infrastructure** group, select the **PE Console** group.
2. On the **Classes** tab, in the `puppet_enterprise::profile::console` class, add the `rbac_session_timeout` parameters, providing the desired value in minutes.
3. Click **Add parameter**, and commit changes.
4. [Run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes) on the node that hosts the console.

## Tuning Java args for the PE console

You can increase the JVM (Java Virtual Machine) memory that is allocated to Java services running on the PE console. This memory allocation is known as the Java heap size.

Instructions for using the PE console to increase the Jave heap size are detailed on on the [Configuring Java Arguments for PE](./config_java_args.html#pe-console-services) page.

## Increasing the ulimit for console services

The various services in PE require up to one file handle per connected client. On most OS configurations, the defaults will not be high enough for more than a couple hundred clients. To support more clients, you need to increate the number of allowed file handles.

Instructions for increasing the number of file handles are detailed on the [Increasing the ulimit for PE services](./config_ulimit.html) page.


## Tuning the classifier synchronization period

This synchronization period controls how long it takes the node classifier (NC) to retrieve classes from the Puppet master, which essentially dictates how soon you can see your changes appear in the NC. By default this value is set to 600 seconds (10 minutes). Depending on the size of your environment(s), this may or may not be a suitable amount of time.

You can change this value in the PE console.

1. In the console, click **Nodes** > **Classification**, and in the **PE Infrastructure** group, select the **PE Console** group.
2. On the **Classes** tab, in the `puppet_enterprise::profile::console` class, modify the `classifier_synchronization_period` parameter, providing a value in seconds.
3. Click **Add parameter**, and commit changes.

You can also force the NC to retrieve classes from the master immediately, by clicking the **Refresh** button.

If you don't ever want to synchronize automatically because you'd like to [trigger synchronization from the endpoint](./nc_update_classes.html#post-v1update-classes), you can set the value to `0` at which point it will never synchronize.

## Tuning the PostgreSQL buffer pool size

If you are experiencing performance issues or instability with the console, you may need to adjust the buffer memory settings for PostgreSQL. The most important PostgreSQL memory settings for PE are `shared_buffers` and `work_mem`.  Generally speaking, you should allocate about 25% of your hardware's RAM to `shared_buffers`. If you have a large and/or complex deployment you will probably need to increase `work_mem` from the default of 1mb. For more detail, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.2/static/runtime-config-resource.html).

The PE PostgreSQL configuration file is located at `/opt/puppetlabs/server/data/postgresql/9.4/data/postgresql.conf`.

After changing any of these settings, restart the PostgreSQL server:

    $ sudo /etc/init.d/pe-postgresql restart

## Changing console timestamps to local time

By default, the console displays timestamps in UTC format (also known as Zulu time). Hover over the timestamp to view a conversion in local time, as determined by your web browser.

If you prefer, you can change your console settings to display all timestamps in local time, with UTC time shown on hover, by following these steps:

1. In the console, click **Nodes** > **Classification**, and in the **PE Infrastructure** group, select the **PE Console** node group.

2. On the **Classes** tab, in the `puppet_enterprise::profile::console` class, specify parameters:

   - **Parameter** -- Select **display_local_time**.

   - **Value** -- Enter **true** to display timestamps in local time, with hover text showing UTC time. The default value, **false**, shows timestamps in UTC time.

3. Click **Add parameter**, and commit changes.

In order for this change to take effect, you must [run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes) on the Puppet master. This Puppet run causes pe-console-services to restart, which also restarts the console. You might need to refresh your browser once the console restarts. 

## Changing the console's port

PE serves the console on SSL port, 443. You can change this port as needed.

1. In the console, click **Nodes** > **Classification**, and in the **PE Infrastructure** group, select the **PE Console** group.
2. On the **Classes** tab, in the `puppet_enterprise::profile::console` class, modify the `console_ssl_listen_port` parameter, providing a value for the new port number.
3. Click **Add parameter**, and commit changes.
4. [Run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes) on the node that hosts the console.

The console should now be available on the port you provided.

## Changing the classifier's API port

PE servers the node classifier API on SSL port, 4433. You can change this port as needed.

1. In the console, cick **Nodes** > **Classification** and select the **PE Infrastructure** group.
2. On the **Classes** tab, under the `puppet_enterprise` class, select the `api_port` parameter from the drop-down list.
3. In the **Value** field, provide a new port number, click **Add parameter**, and commit changes.4. [Run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes) on the node that hosts the console.

## Changing PE's nginx listen address

PE's nginx listen address (for the PE console) defaults to `0.0.0.0`. You can use the `ssl_listen_address` parameter in the `puppet_enterprise::profile::console` class to change this.

1. In the console, click **Nodes** > **Classification**, and in the **PE Infrastructure** group, select the **PE Console** group.
2. On the **Classes** tab, in the `puppet_enterprise::profile::console` class, modify the `ssl_listen_address` parameter, providing a value for the new address.
3. Click **Add parameter**, and commit changes.
4. [Run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes) on the node that hosts the console.

## Disabling update checking

You can [disable automatic update checks](./config_puppetserver.html#disabling-update-checking) via the `pe-puppetserver` service.

## Pruning the node classifier database

The size of the node classifier database increases over time as PE runs and agents continue to check in. PE periodically prunes node check-ins from the node classifier database to prevent it from becoming too large. The pruning process runs every 24 hours (and each time pe-console-services starts or is restarted), and it deletes node check-in history that is older than the default of seven days.

To change the default number of days:

1. In the console, click **Nodes** > **Classification**, and in the **PE Infrastructure** group, select the **PE Console** group.
2. On the **Classes** tab, in the `puppet_enterprise::profile::console` class, modify the `classifier_prune_threshold` parameter, providing a value for the number of days.
3. Click **Add parameter**, and commit changes.
4. [Run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes) on the node that hosts the console.

**Note:** If you set the `classifier_prune_threshold` value to 0, the node classifier service will never prune the database.

## Changing the unresponsive node timeframe

By default, PE considers a node unresponsive after one hour. This timeframe can be configured in the `console.conf` file at `/etc/puppetlabs/console-services/conf.d` by adding `no-longer-reporting-cutoff` and expressing the desired timeframe in seconds:

		console: {
		...
  			no-longer-reporting-cutoff: 3600
		...
		}

