---
layout: default
title: "Configuring and tuning Puppet Server"
canonical: "/pe/latest/config_puppetserver.html"
---

After you've installed Puppet Enterprise (PE), optimize it for your environment by configuring and tuning Puppet Server settings as needed.

See [Tuning monolithic installations](./config_monolithic.html) for recommended settings based on various monolithic installation sizes.

## Tune the maximum number of JRuby instances

The `jruby_max_active_instances` setting controls the maximum number of JRuby instances to allow on the Puppet Server. The default used in PE is the number of CPUs - 1, expressed as `$::processorcount` - 1. One instance is the minimum value and four instances is the maximum value. Four JRuby instances will work for most environments. However, if you're running a large number of nodes, you might need to increase the number of JRuby instances up to `$::processorcount - 1`.

Increasing the JRuby instances increases the amount of RAM used by `pe-puppetserver`. When you increase JRuby instances, increase the [heap size](./config_java_args.html#pe-puppet-server-service). We conservatively estimate that a JRuby process uses 512MB of RAM.

1. Increase or decrease the number of JRuby instances by [editing your Hiera default `.yaml` file](./config_intro.html#configure-settings-with-hiera), tuning the `jruby_max_active_instances` by setting the following code:

    `puppet_enterprise::master::puppetserver::jruby_max_active_instances: <number of instances>`

## Tune the Java heap size

Java heap size is the JVM (Java Virtual Machine) memory allocated to Java services running on Puppet Server.

See instructions to [change the Java heap size](./config_java_args.html#pe-puppet-server-service).

## Tune `ruby_load_path`

The `ruby_load_path` setting determines where the Puppet Server finds Puppet, Facter, and so on. The default setting is located here: `$puppetserver_jruby_puppet_ruby_load_path = ['/opt/puppetlabs/puppet/lib/ruby/vendor_ruby', '/opt/puppetlabs/puppet/cache/lib']`.

1. Override this setting by changing the path to a different array in your Hiera configuration:

    `puppet_enterprise::master::puppetserver::puppetserver_jruby_puppet_ruby_load_path`

Note that if you change the `libdir` you must also change the `vardir`.

## Tune `max_requests_per_instance`

The `max_requests_per_instance` setting determines the maximum number of requests per instance of a JRuby interpreter before it is killed. The appropriate value for this parameter depends on how busy your servers are and how much you are affected by a memory leak.

When a JRuby interpreter is killed, all of its memory is reclaimed and it is replaced in the pool with a new JRuby interpreter. This prevents any one JRuby interpreter from consuming too much RAM, mitigating Puppet code memory leak issues and keeping Puppet Server up.

Starting a new interpreter has a performance cost, so set the parameter to get a new interpreter no more than every few hours. There are multiple JRuby interpreters running with requests balanced across them, so the lifespan of each interpreter varies. On a server with `jruby_max_active_instances` set to five, and an average concurrency of five to eight nodes active at a time, the default value of 10,000 provides a five hour lifespan per JRuby instance.

1. Increase `max_requests_per_instance` by [editing your Hiera default `.yaml` file](./config_intro.html#configure-settings-with-hiera) with the following code:

    `puppet_enterprise::master::puppetserver::jruby_max_requests_per_instance: <number of requests>`

## Enable or disable `environment-class-cache-enabled`

The optional `environment-class-cache-enabled` setting specifies whether cached data is used when updating classes in the console. When `true`, Puppet Server refreshes classes using file sync, improving performance.

The default value [for `environment-class-cache-enabled`]({{puppetserver}}/config_file_puppetserver.html#settings) depends on whether you use Code Manager:

- With Code Manager, the default value is enabled (`true`). File sync clears the cache automatically in the background, so clearing the environment cache manually isn't required when using Code Manager.

- Without Code Manager, the default value is disabled (`false`).

> **Note:** If you're not using Code Manager and opt to enable this setting, make sure your code deployment method -- for example r10k -- clears the environment cache when it completes. If you don't clear the environment cache, the Node Classifier doesn't receive new class information until Puppet server is restarted.

1. Enable or disable `environment-class-cache-enabled` by [editing your Hiera default `.yaml` file](./config_intro.html#configure-settings-with-hiera) with the following code:

     `puppet_enterprise::master::puppetserver::jruby_environment_class_cache_enabled: <true OR false>`

## Increase the ulimit

Services in PE require up to one file handle per connected client. On most OS configurations, the defaults are not high enough for more than a couple of hundred clients. Support more clients by increasing the number of allowed file handles.

View instructions to [increase the ulimit for PE services](./config_ulimit.html).

## Understanding the `environment_timeout` setting

The `environment_timeout` setting controls how long the Puppet master caches data it loads from an environment, determining how much time passes before changes to an environment's Puppet code are reflected in its environment.

In PE, the [`environment_timeout`]({{puppet}}/environments_configuring.html#environmenttimeout) is set to `unlimited` for performance. We recommend leaving this setting as is.

Read more about [environments and their limitations]({{puppet}}/environments_limitations.html#environments:-limitations-of-environments).

## Add certificates to the `puppet-admin` certificate whitelist

Modify the `puppet-admin` certificate whitelist as needed in Hiera.

1. [Edit your Hiera default `.yaml` file](./config_intro.html#configure-settings-with-hiera) with the following code:

~~~
puppet_enterprise::master::puppetserver::puppet_admin_certs:
 - 'example_cert_name'
~~~

## Disable update checking

Puppet Server (pe-puppetserver) checks for updates when it starts or restarts, and every 24 hours thereafter. It transmits basic, anonymous info to our servers at Puppet to get update information.

Specifically, it transmits:

* Product name
* Puppet Server version
* IP address
* Data collection timestamp

1. Disable update checking by adding the following parameter to either the **PE Master** node group in the node classifier or to your [Hiera configuration](./config_intro.html#configure-settings-with-hiera):

   ```
   puppet_enterprise::profile::master::check_for_updates: false
   ```

2. On your Puppet master node, run Puppet.

## Puppet Server configuration files

At startup, Puppet Server reads all of the `.conf` files in the `conf.d` directory (`/etc/puppetlabs/puppetserver/conf.d`). The `conf.d` directory contains the following files:

<table>
  <tr>
    <th>File name</th>
    <th>Description</th>
    <th>Details</th>
  </tr>
  <tr>
    <td><code>auth.conf</code></td>
    <td>Contains authentication rules and settings for agents and API endpoint access.</td>
    <td><a href="/puppetserver/latest/config_file_auth.html">Puppet Server documentation</a></td>
  </tr>
  <tr>
    <td><code>global.conf</code></td>
    <td>Contains global configuration settings for Puppet Server, including <a href="/puppetserver/latest/config_file_logbackxml.html">logging settings</a>.</td>
    <td><a href="/puppetserver/latest/config_file_global.html">Puppet Server documentation</a></td>
  </tr>
  <tr>
    <td><code>metrics.conf</code></td>
    <td>Contains settings for Puppet Server metrics services.</td>
    <td><a href="./puppet_server_metrics.html">Graphing Puppet server metrics</a></td>
  </tr>
  <tr>
    <td><code>pe-puppet-server.conf</code></td>
    <td>Contains Puppet Server settings specific to Puppet Enterprise.</td>
    <td><a href="#pe-puppet-server-conf-settings"><code>pe-puppet-server.conf</code> settings</a></td>
  </tr>
  <tr>
    <td><code>webserver.conf</code></td>
    <td>Contains SSL and external Certificate Authority service configuration settings.</td>
    <td><a href="/puppetserver/latest/config_file_webserver.html">Puppet Server documentation</a></td>
  </tr>
  <tr>
    <td><code>ca.conf</code></td>
    <td>(Deprecated) Contains rules for Certificate Authority services. Superseded by <code>webserver.conf</code> and <code>auth.conf</code>.</td>
    <td><a href="/puppetserver/latest/config_file_ca.html">Puppet Server documentation</a></td>
  </tr>
</table>

## `pe-puppet-server.conf` settings

This file contains Puppet Server settings specific to Puppet Enterprise, with all settings wrapped in a `jruby-puppet` section.

<table>
  <tr>
    <th>Setting</th>
    <th>Description</th>
    <th>Default value</th>
  </tr>
  <tr>
    <td><code>gem-home</code></td>
    <td>Determines where JRuby looks for gems. It is also used by the puppetserver gem command line tool.</td>
    <td><code>/opt/puppetlabs/puppet/cache/jruby-gems</code></td>
  </tr>
  <tr>
    <td><code>master-conf-dir</code></td>
    <td>Sets the Puppet configuration directory's path.</td>
    <td><code>/etc/puppetlabs/puppet</code></td>
  </tr>
  <tr>
    <td><code>master-var-dir</code></td>
    <td>Sets the Puppet variable directory's path.</td>
    <td><code>/opt/puppetlabs/server/data/puppetserver</code></td>
  </tr>
  <tr>
    <td><code>ruby-load-path</code></td>
    <td>Sets the Puppet configuration directory's path. The agent's <code>libdir</code> value is added by default.</td>
    <td><code>'/opt/puppetlabs/puppet/lib/ruby/vendor_ruby', '/opt/puppetlabs/puppet/cache/lib'</code></td>
  </tr>
</table>