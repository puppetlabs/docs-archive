---
layout: default
title: "Configuring and tuning the console"
canonical: "/pe/latest/console_config.html"
---

After installing PE, you can change product settings to customize the console behavior, adjust to your team's needs, and improve performance.

## Configure the PE console and console-services

There are several parameters you can add to configure the behavior of the PE console and console-services.

1. In the console, click **Classification**, and in the **PE Infrastructure** group, select the **PE Console** group.
2. On the **Classes** tab, locate the `puppet_enterprise` class indicated and add any of the following parameters and values as needed.

   <table>
    <tr>
      <th>Parameter</th>
      <th>Value</th>
    </tr>
    <tr>
      <td nowrap><code>puppet_enterprise::profile::console::classifier_synchronization_period</code></td>
      <td nowrap>Must be an integer. Classifier synchronization period.<br> Controls how long it takes the node classifier (NC) to retrieve classes <br> from the Puppet master. Default is <code>"600"</code> seconds (10 minutes).</td>
    </tr>
    <tr>
      <td nowrap><code>puppet_enterprise::api_port</code></td>
      <td>The SSL port PE serves the node classifier on. Defaults to <code>"[4433]"</code>.</td>
     </tr>
     <tr>
      <td nowrap><code>puppet_enterprise::profile::console::rbac_session_timeout</code></td>
      <td>Must be an integer. Specifies how long a user's session should last, in minutes. This session is the same across node classification, RBAC, and the console. The default value is <code>"60"</code>.</td>
     </tr>
     <tr>
       <td nowrap><code>puppet_enterprise::profile::console::session_maximum_lifetime</code></td>
       <td>Must be an integer. Specifies the maximum allowable period that a console session can be valid for. Supported units are "s" (seconds), "m" (minutes), "h" (hours), "d" (days), "y" (years). May be set to "0" to not expire before the maximum token lifetime. <br>Units are specified as a single letter following an integer, for example <code>"1d"</code> (1 day). If no units are specified, the integer is treated as seconds.</td>
     </tr>
     <tr>
       <td nowrap><code>puppet_enterprise::profile::console::console_ssl_listen_port</code></td>
       <td>Must be an integer. The port the console is available on.<br> Default is <code>"[443]"</code>.</td>
     </tr>
     <tr>
       <td nowrap><code>puppet_enterprise::profile::console::ssl_listen_address</code></td>
       <td>PE's nginx listen address for the PE console. <br>Defaults to <code>"0.0.0.0"</code>.</td>
      </tr>
      <tr>
       <td nowrap><code>puppet_enterprise::profile::console::classifier_prune_threshold</code></td>
       <td>Must be an integer. The number of days to wait before pruning the size of the classifier database. <br>If you set the value to <code>"0"</code>, the node classifier service will never prune the database.</td>
      </tr>
      <tr>
        <td nowrap><code>puppet_enterprise::profile::console::display_local_time</code></td>
        <td>By default, the console displays timestamps in UTC format (also known as Zulu time). If you prefer, you can change your console settings to display all timestamps in local time, with UTC time shown on hover. Set to <code>true</code> to display timestamps in local time, with hover text showing UTC time or <code>false</code> (default) to show timestamps in UTC time.</td>
     </tr>
   <tr>
     <td nowrap><code>puppet_enterprise::profile::console::classifier_node_check_in_storage</code></td>
     <td>The node classifier service can store a check-in for each node when its classification is requested that includes an explanation of how it matched the rule of every group it was classified into. This is disabled by default. Default is <code>false</code>. Set to <code>true</code>to enable.</td>
   </tr>
   <tr>
     <td nowrap><code>puppet_enterprise::console_services::no_longer_reporting_cutoff</code></td>
     <td>Determines the amount of time that should pass after sending its last report before a node is considered unresponsive. Set an integer to specify the value in seconds. Default is 3600 seconds.</td>
   </tr>       
   </table>

3. Click **Add parameter** as needed, and commit changes.
4. On the nodes hosting the Puppet master and PE console, [run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes).

## Change password reset and lockout settings

You can change the default settings for how long new password change tokens last, and how many failed login attempts are permitted.

When a user doesn’t remember their current password, an administrator can generate a token for them to change their password. The duration, in hours, that this generated token is valid can be changed with the `password-reset-expiration` parameter. The default value is 24.

The `failed-attempts-lockout` parameter takes a positive integer that specifies how many failed login attempts are allowed on an account before that account is revoked. The default value is 10.

1. **On the node hosting the PE console,** navigate to `/etc/puppetlabs/console-services/conf.d` and create a new file.

   >**Note:** The file name is arbitrary, but the file format is [HOCON](./config_hocon.html).

2. In the file, add the following lines with the values you want:

   ~~~
   rbac: {
    password-reset-expiration: 24
    failed-attempts-lockout: 10
   }
   ~~~

3. Reload pe-console-services:

   ~~~
   sudo service pe-console-services reload
   ~~~

## Disable HTTPS redirect

The console redirects to HTTPS when you attempt to connect over HTTP. The pe-nginx webserver now listens on port 80 by default.

To disable the HTTPS redirect, edit your Hiera .yaml file to add the following setting:

~~~
puppet_enterprise::profile::console::proxy::http_redirect::enable_http_redirect: false
~~~

The Hiera .yaml default location is `/etc/puppetlabs/code/environments/%{environment}/hieradata` (for *nix) and `%CommonAppData%\PuppetLabs\code\environments\%{environment}\hieradata` (for Windows). 

## Tuning the PostgreSQL buffer pool size

If you are experiencing performance issues or instability with the console, you may need to adjust the buffer memory settings for PostgreSQL. The most important PostgreSQL memory settings for PE are `shared_buffers` and `work_mem`.

1. Open the PE PostgreSQL configuration file: `/opt/puppetlabs/server/data/postgresql/9.4/data/postgresql.conf`.
2. Change the `shared_buffers` setting so that it is about 25 percent of your hardware's RAM.
3. If you have a large or complex deployment, increase the `work_mem` value from the default of 1MB.
4. Restart the PostgreSQL server by running: `sudo /etc/init.d/pe-postgresql restart`

For more detail, see the [PostgreSQL documentation](http://www.postgresql.org/docs/9.2/static/runtime-config-resource.html).


*********

**Related Links**

- Disable update checking

  You can [disable automatic update checks](./config_puppetserver.html#disabling-update-checking) via the `pe-puppetserver` service.

- Tune Java args for the PE console

   You can increase the JVM (Java Virtual Machine) memory that is allocated to Java services running on the PE console. This memory allocation is known as the Java heap size.

   Instructions for using the PE console to increase the Jave heap size are detailed on on the [Configuring Java Arguments for PE](./config_java_args.html#pe-console-services) page.

- Increase the ulimit for console services

   The various services in PE require up to one file handle per connected client. On most OS configurations, the defaults will not be high enough for more than a couple hundred clients. To support more clients, you need to increate the number of allowed file handles.

   Instructions for increasing the number of file handles are detailed on the [Increasing the ulimit for PE services](./config_ulimit.html) page.
