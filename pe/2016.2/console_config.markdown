---
layout: default
title: "Configuring and tuning the console"
canonical: "/pe/latest/console_config.html"
---

## Changing session duration

There are a few options that can be configured for access control:

- `password-reset-expiration`  When a user doesn't remember their current password, an administrator can generate a token for them to change their password. The duration, in hours, that this generated token is valid can be changed with this config parameter. The default value is 24.
- `session-timeout`  This parameter is a positive integer that specifies how long a user's session should last, in minutes. This session is the same across node classification, RBAC, and the console. The default value is 60.
- `failed-attempts-lockout`  This parameter is a positive integer that specifies how many failed login attempts are allowed on an account before that account is revoked. The default value is 10.

To change these defaults, add a new file to the `/etc/puppetlabs/console-services/conf.d` directory. The file name is arbitrary, but the file format is [HOCON](./config_hocon.html).

    rbac: {
     password-reset-expiration: 24
     session-timeout: 60
     failed-attempts-lockout: 10
    }

Then restart pe-console-services (`sudo service pe-console-services restart`).

## Tuning Java args for the PE console

You can increase the JVM (Java Virtual Machine) memory that is allocated to Java services running on the PE console. This memory allocation is known as the Java heap size.

Instructions for using the PE console to increase the Jave heap size are detailed on on the [Configuring Java Arguments for PE](./config_java_args.html#pe-console-services) page.

## Increasing the ulimit for pe-console-services

The various services in PE require up to one file handle per connected client. On most OS configurations, the defaults will not be high enough for more than a couple hundred clients. To support more clients, you need to increate the number of allowed file handles.

Instructions for increasing the number of file handles are detailed on the [Increasing the ulimit for PE services](./config_ulimit.html) page.

## Tuning the classifier synchronization period

This synchronization period controls how long it takes the node classifier (NC) to retrieve classes from the Puppet master, which essentially dictates how soon you can see your changes appear in the NC. By default this value is set to 600 seconds (10 minutes). Depending on the size of your environment(s), this may or may not be a suitable amount of time.

You can change this value in the PE console.

1. In the PE console, navigate to the **Classification** page.
2. Select the **PE Console** group.
3. In the **PE Console** group page, click the **Classes** tab.
4. Locate the **puppet_enterprise::profile::console** class, and from the **Parameter** drop-down list, select **classifier_synchronization_period**.
5. In the **Value** field, adjust the period, in seconds.
6. Click **Add parameter**, and then the **Commit change** button.

You can also force the NC to retrieve classes from the master immediately, by clicking the **Refresh** button.

If you don't ever want to synchronize automatically because you'd like to [trigger synchronization from the endpoint](./nc_update_classes.html#post-v1update-classes), you can set the value to `0` at which point it will never synchronize.

## Tuning the PostgreSQL buffer pool size

If you are experiencing performance issues or instability with the console, you may need to adjust the buffer memory settings for PostgreSQL. The most important PostgreSQL memory settings for PE are `shared_buffers` and `work_mem`.  Generally speaking, you should allocate about 25% of your hardware's RAM to `shared_buffers`. If you have a large and/or complex deployment you will probably need to increase `work_mem` from the default of 1mb. For more detail, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.2/static/runtime-config-resource.html).

The PE PostgreSQL configuration file is located at `/opt/puppetlabs/server/data/postgresql/9.4/data/postgresql.conf`.

After changing any of these settings, restart the PostgreSQL server:

    $ sudo /etc/init.d/pe-postgresql restart

## Changing the console's port

By default, a new installation of PE will serve the console on the default SSL port, 443. If you wish to change the port the console is available on:

1. In the PE console, navigate to the **Classification** page.
2. Select the **PE Console** group, and then click the **Classes** tab.
3. Locate the `puppet_enterprise::profile::console` class, and from the **Parameter** drop-down list, select **console_ssl_listen_port**.
4. In the **Value** field, add the new port number.
5. Click **Add parameter**, and then the **Commit change** button.
6. Trigger a Puppet run on the node hosting the console. 

The console should now be available on the port you provided.

## Changing PE's nginx listen address

PE's nginx listen address (for the PE console) defaults to `0.0.0.0`. You can use the `ssl_listen_address` parameter in the `puppet_enterprise::profile::console` class to change this. 

1. In the PE console, navigate to the **Classification** page.
2. Select the **PE Console** group, and then click the **Classes** tab.
3. Locate the `puppet_enterprise::profile::console` class, and from the **Parameter** drop-down list, select **ssl_listen_address**.
4. In the **Value** field, add the new address.
5. Click **Add parameter**, and then the **Commit change** button.
6. Trigger a Puppet run on the node hosting the console. 

## Disabling update checking

[Disabling updated checking](./config_puppetserver.html#disabling-update-checking) is handled via the `pe-puppetserver` service.

## Pruning the node classifier database

The size of the node classifier database increases over time as PE runs and agents continue to check in. PE periodically prunes node check-ins from the node classifier database to prevent it from becoming too large. The pruning process runs every 24 hours (and each time pe-console-services starts or is restarted), and it deletes node check-in history that is older than the default of seven days.

To change the default number of days:

1. In the PE console, navigate to the **Classification** page (**Nodes** > **Classification**).
2. Click the **PE Console** group.
3. In the **PE Console** group page, click the **Classes** tab.
4. Locate the `puppet_enterprise::profile::console` class, and from the Parameter drop-down list, select `classifier_prune_threshold`.
5. In the **Value** field, enter the number of days that you want to use as the default number.
6. Click **Add parameter** and then click the **Commit change** button."
7. To make the change take affect, run Puppet (`puppet agent -t`). Running Puppet will restart pe-console-services.

**Note:** If you set the `classifier_prune_threshold` value to 0, the node classifier service will never prune the database.

## Changing the unresponsive node timeframe

By default, PE considers a node unresponsive after one hour. This timeframe can be configured in the `console.conf` file at `/etc/puppetlabs/console-services/conf.d` by adding `no-longer-reporting-cutoff` and expressing the desired timeframe in seconds:

		console: {
		...
  			no-longer-reporting-cutoff: 3600
		...
		}

