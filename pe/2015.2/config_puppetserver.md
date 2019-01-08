---
layout: default
title: "PE 2015.2 » Configuration"
subtitle: "Configuring & Tuning Puppet Server"
canonical: "/pe/latest/config_puppetserver.html"
---

We recommend tuning the following settings as needed to improve the performance of your Puppet Server.

## Tuning JRuby on Puppet Server

This setting controls the maximum number of JRuby instances to allow on the Puppet Server. The default used in PE 2015.2 is the number of CPUs - 1, which is expressed as `$::processorcount` - 1. One instance is the minimum value and four instances is the maximum value.

Four JRuby processes will work for most environments; however, if you're running a higher number of nodes, you may need to provide more JRuby processes up to `$::processorcount - 1`. Increasing the JRuby processes does increase the amount of RAM used by `pe-puppetserver` so you will also need to increase the [heap size](./config_java_args.html#pe-puppet-server-service) as you increase JRuby processes. We conservatively estimate that a JRuby process uses 512MB of RAM.

To increase or decrease the number of JRuby instances, you need to [edit your Hiera default `.yaml` file](./config_intro.html#configure-settings-with-hiera), and tune the `jruby_max_active_instances` by setting the following code:

	puppet_enterprise::master::puppetserver::jruby_max_active_instances: <number of instances>

## Tuning Puppet's `ruby_load_path` Setting

The `ruby_load_path` setting determines where the Puppet Server finds Puppet, Facter, and so on. The default setting is located here: `$puppetserver_jruby_puppet_ruby_load_path = ['/opt/puppetlabs/puppet/lib/ruby/vendor_ruby', '/opt/puppetlabs/puppet/cache/lib']`.

To override this setting, set the following path to a different array in your hiera configuration:

`puppet_enterprise::master::puppetserver::puppetserver_jruby_puppet_ruby_load_path`

Note that if you change the `libdir` you must also change the `vardir`.

## Tuning `max_requests_per_instance` on Puppet Server

> **Note:** Don't use the max_requests_per_instance setting in PE 2015.2.3. Doing so causes a memory leak that destabilizes the server over time. For details, see the [Puppet Server Release Notes](/puppetserver/2.2/release_notes.html#max-requests-per-instance-causes-a-memory-leak).

This setting allows you to set the maximum number of requests per instance of a JRuby interpreter before it is killed. When it is killed, all of its memory is reclaimed and is then replaced in the pool with a fresh new JRuby interpreter. This helps to mitigate memory leak issues in Puppet code and to generally keep the server up by avoiding a situation where any one JRuby interpreter consumes too much ram.

The appropriate value for this parameter will depend on how busy your servers are and how badly you are being affected by a memory leak. Since there is a performance cost to starting a new interpreter, we recommend you set this such that you get a new interpreter no more than every few hours. In practice, on a server with `jruby_max_active_instances` set to five and an average concurrency of five to eight nodes active at a time, a value of 10,000 (the default value) provided a five hour lifespan per JRuby instance. Since there are multiple JRuby interpreters running, with requests balanced across them, the actual lifespan of a given interpreter may be somewhat variable.

To increase the max threads, [edit your Hiera default `.yaml` file](./config_intro.html#configure-settings-with-hiera) with the following code:

         puppet_enterprise::master::puppetserver::jruby_max_requests_per_instance: <number of requests>

## Increasing the ulimit for the `pe-puppet` User

The ulimit controls the number of processes and file handles that the `pe-puppet` user can open/process. To increase the ulimit for the `pe-puppet` user, edit `/etc/security/limits.conf` so that it contains the following:

     pe-puppet hard	nofile      65535

## Tuning Java Args for Puppet Server

You can increase the JVM (Java Virtual Machine) memory that is allocated to Java services running on Puppet Server. This memory allocation is known as the Java heap size.

Instructions for using the PE console to increase the Java heap size are detailed on on the [Configuring Java Arguments for PE](./config_java_args.html#pe-puppet-server-service) page.

## Tuning the Environment Timeout

The [setting](/puppet/4.2/reference/environments_configuring.html#environmenttimeout) for `environment_timeout` controls how long the Puppet master caches data it loads from an environment. When you make changes to an environment's Puppet code, this setting determines how much time will pass before those changes are reflected in the environment. Please review the `environment_timeout` documentation, as you will probably want to tune this setting per environment depending on your needs.

There is a CPU cost to refresh the environment cache, so when tuning for performance we advise you set `environment_timeout` to `unlimited`. This will generally improve catalog compilation times by never refreshing the environment cache. As mentioned in the linked docs you will need a way to flush this cache when you want Puppet code updates to take effect.

You can also read more about [environments and their limitations](/puppet/4.2/reference/environments_limitations.html#environments:-limitations-of-environments).

## Adding Certificates to the Puppet Server `puppet-admin` Certificate Whitelist

You can add certificates to the Puppet Server `puppet-admin` Certificate Whitelist using Hiera, as shown in the following example. Note that you cannot modify the whitelist using the PE node classifier.

To modify the whitelist using Hiera, [edit your Hiera default `.yaml` file](./config_intro.html#configure-settings-with-hiera) with the following code:

~~~
puppet_enterprise::master::puppetserver::puppet_admin_certs:
 - 'example_cert_name'
~~~

Replace `example_cert_name` with the name of the certificate you're adding.
