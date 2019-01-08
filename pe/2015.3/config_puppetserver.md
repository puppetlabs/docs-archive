---
layout: default
title: "Configuring & Tuning Puppet Server"
canonical: "/pe/latest/config_puppetserver.html"
---

We recommend tuning the following settings as needed to improve the performance of your Puppet Server.

## Tuning JRuby on Puppet Server

This setting controls the maximum number of JRuby instances to allow on the Puppet Server. The default used in PE 2015.3 is the number of CPUs - 1, which is expressed as `$::processorcount` - 1. One instance is the minimum value and four instances is the maximum value.

Four JRuby processes will work for most environments; however, if you're running a higher number of nodes, you may need to provide more JRuby processes up to `$::processorcount - 1`. Increasing the JRuby processes does increase the amount of RAM used by `pe-puppetserver` so you will also need to increase the [heap size](./config_java_args.html#pe-puppet-server-service) as you increase JRuby processes. We conservatively estimate that a JRuby process uses 512MB of RAM.

To increase or decrease the number of JRuby instances, you need to [edit your Hiera default `.yaml` file](./config_intro.html#configure-settings-with-hiera), and tune the `jruby_max_active_instances` by setting the following code:

    puppet_enterprise::master::puppetserver::jruby_max_active_instances: <number of instances>

## Tuning Puppet's `ruby_load_path` Setting

The `ruby_load_path` setting determines where the Puppet Server finds Puppet, Facter, and so on. The default setting is located here: `$puppetserver_jruby_puppet_ruby_load_path = ['/opt/puppetlabs/puppet/lib/ruby/vendor_ruby', '/opt/puppetlabs/puppet/cache/lib']`.

To override this setting, set the following path to a different array in your hiera configuration:

    puppet_enterprise::master::puppetserver::puppetserver_jruby_puppet_ruby_load_path

Note that if you change the `libdir` you must also change the `vardir`.

## Tuning `max_requests_per_instance` on Puppet Server

This setting allows you to set the maximum number of requests per instance of a JRuby interpreter before it is killed. When it is killed, all of its memory is reclaimed and is then replaced in the pool with a fresh new JRuby interpreter. This helps to mitigate memory leak issues in Puppet code and to generally keep the server up by avoiding a situation where any one JRuby interpreter consumes too much ram.

The appropriate value for this parameter will depend on how busy your servers are and how badly you are being affected by a memory leak. Since there is a performance cost to starting a new interpreter, we recommend you set this such that you get a new interpreter no more than every few hours. In practice, on a server with `jruby_max_active_instances` set to five and an average concurrency of five to eight nodes active at a time, a value of 10,000 (the default value) provided a five hour lifespan per JRuby instance. Since there are multiple JRuby interpreters running, with requests balanced across them, the actual lifespan of a given interpreter may be somewhat variable.


To increase the max threads, [edit your Hiera default `.yaml` file](./config_intro.html#configure-settings-with-hiera) with the following code:

    puppet_enterprise::master::puppetserver::jruby_max_requests_per_instance: <number of requests>


## Increasing the ulimit for pe-puppetserver

The various services in PE require up to one file handle per connected client. On most OS configurations, the defaults will not be high enough for more than a couple hundred clients. To support more clients, you need to increate the number of allowed file handles.

Instructions for increasing the number of file handles are detailed on the [Increasing the ulimit for PE services](./config_ulimit.html) page.

## Tuning Java Args for Puppet Server

You can increase the JVM (Java Virtual Machine) memory that is allocated to Java services running on Puppet Server. This memory allocation is known as the Java heap size.

Instructions for using the PE console to increase the Java heap size are detailed on on the [Configuring Java Arguments for PE](./config_java_args.html#pe-puppet-server-service) page.

## Tuning the Environment Timeout

The [setting](/puppet/4.3/reference/environments_configuring.html#environmenttimeout) for `environment_timeout` controls how long the Puppet master caches data it loads from an environment. When you make changes to an environment's Puppet code, this setting determines how much time will pass before those changes are reflected in the environment. Please review the `environment_timeout` documentation, as you will probably want to tune this setting per environment depending on your needs.

There is a CPU cost to refresh the environment cache, so when tuning for performance we advise you set `environment_timeout` to `unlimited`. This will generally improve catalog compilation times by never refreshing the environment cache. As mentioned in the linked docs you will need a way to flush this cache when you want Puppet code updates to take effect.

You can also read more about [environments and their limitations](/puppet/4.3/reference/environments_limitations.html#environments:-limitations-of-environments).

## Adding Certificates to the Puppet Server `puppet-admin` Certificate Whitelist

You can add certificates to the Puppet Server `puppet-admin` Certificate Whitelist using Hiera, as shown in the following example. Note that you cannot modify the whitelist using the PE node classifier.

To modify the whitelist using Hiera, [edit your Hiera default `.yaml` file](./config_intro.html#configure-settings-with-hiera) with the following code:

~~~
puppet_enterprise::master::puppetserver::puppet_admin_certs:
 - 'example_cert_name'
~~~

Replace `example_cert_name` with the name of the certificate you're adding.

## Disabling Update Checking

When Puppet Server (`pe-puppetserver`) starts or restarts, it checks for updates. To get the correct update info, the server will pass some basic, anonymous info to Puppet Labs' servers. Specifically, it will transmit:

* the IP address of the client
* the type and version of the client's OS
* the installed version of PE

There are two methods to disable update checking (e.g. if your company policy forbids transmitting this information), one of which is specific to platforms using systemd (currently RHEL-7 and SLES-12 only).

* If your platform **DOES NOT** use systemd, you can disable update checks by adding the following line to the `/etc/puppetlabs/installer/answers.install` file:

    `q_pe_check_for_updates=n`

   **Note**: if you delete the `/etc/puppetlabs/installer/answers.install` file, update checking will resume.

* If your platform uses systemd, you can disable updates by creating the following file:

    `/etc/puppetlabs/puppetserver/opt-out`

  As long as this file is present, Puppet Server will not check in for updates. (Note that this method will work on all platforms, not just those using systemd.)

