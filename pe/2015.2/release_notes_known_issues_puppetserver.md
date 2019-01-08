---
title: "PE 2015.2 » Release Notes >> Known Issues"
layout: default
subtitle: "Puppet Server Known Issues"
canonical: "/pe/latest/release_notes_known_issues_puppetserver.html"
---

As we discover them, this page will be updated with known issues related to Puppet Server in Puppet Enterprise 2015.2 releases. Fixed issues will be removed from this page and noted in the Bug Fixes section of the release notes. If you find new problems yourself, please file bugs in [our issue tracker](https://tickets.puppetlabs.com).

> **Tip**: More information about Puppet Server can be found in the [Puppet Server docs](/puppetserver/1.0/services_master_puppetserver.html). Differences between PE and open source versions of Puppet Server are typically called out.

## Puppet Server Developments Impact Custom Certificate Extensions

A certificate generated with custom extensions in PE 2015.2.x might not be properly decodable if used in a prior PE release.

## HTTP metrics not tracked for 3.8.x agents

If you are using the [Puppet Server Metrics](./puppet_server_metrics.html), note that the `puppetlabs.<server-id>.http.*` graphs will only include statistics about PE 2015.x agents, not PE 3.x agents. <!--PE-10705-->

## Updating Puppet Master Gems

If you've installed any additional Ruby gems beyond those installed by PE, they will not be migrated to the Puppet master during the upgrade to PE 3.8. If you have modules that depend on additional gems, you will need to install them on the Puppet master after you complete the upgrade process.

You can update the gems used by your Puppet master with `/opt/puppetlabs/bin/puppetserver gem install <GEM NAME>`.

After updating the gems, you need to restart the Puppet master with `service pe-puppetserver restart`. You should do this **before** doing any Puppet agent runs.

>**Note**: Be sure you run as `root` when installing `puppetserver` gems.

## Installing Gems when Puppet Server is Behind a Proxy Requires Manual Download of Gems

If you run Puppet Server behind a proxy, the `puppetserver gem install` command will fail. Instead you can install the gems as follows:

1. Use [rubygems.org](https://rubygems.org/pages/download#formats) to search for and download the gem you want to install, and then transfer that gem to your Puppet master.
2. Run `/opt/puppetlabs/bin/puppetserver gem install --local <PATH to GEM>`.

## A Note About Gems with Native (C) Extensions for JRuby on the Puppet Server

Please see the Puppet Server documentation for a description of this issue, [Gems with Native (C) Extensions](/puppetserver/1.0/gems.html#gems-with-native-c-extensions).

## Puppet Server Run Issue when `/tmp/` Directory Mounted `noexec`

In some cases (especially for RHEL 7 installations) if the `/tmp` directory is mounted as `noexec`, Puppet Server might fail to run correctly, and you might see an error in the Puppet Server logs similar to the following:

    Nov 12 17:46:12 fqdn.com java[56495]: Failed to load feature test for posix: can't find user for 0
    Nov 12 17:46:12 fqdn.com java[56495]: Cannot run on Microsoft Windows without the win32-process, win32-dir and win32-service gems: Win32API only supported on win32
    Nov 12 17:46:12 fqdn.com java[56495]: Puppet::Error: Cannot determine basic system flavour

To work around this issue, you can either mount the `/tmp` directory without `noexec`, or you can choose a different directory to use as the temporary directory for the Puppet Server process.

If you want to use a directory other than `/tmp`, you can add an extra argument to the `$java_args` parameter of the `puppet_enterprise::profile::master` class using the PE console. Add `{"Djava.io.tmpdir=/var/tmp":""}` as the value for the `$java_args` parameter. Refer to [Editing Parameters](./console_classes_groups_making_changes.html#editing-parameters) for instructions on editing parameters in the console.

If `pe-puppetserver` has crashed and you can’t access the PE console, you can add the extra argument as follows:

1. Stop `puppet` by running:

    `puppet resource service puppet ensure=stopped`

2. Add  `-Djava.io.tmpdir=/var/tmp` to `JAVA_ARGS` in `/etc/sysconfig/pe-puppetserver` similar to the following: `JAVA_ARGS="-Xms2048m -Xmx2048m -XX:MaxPermSize=256m -Djava.io.tmpdir=/var/tmp"`.

3. Restart `pe-puppetserver` by running:

    `puppet resource service pe-puppetserver ensure=stopped`
    `puppet resource service pe-puppetserver ensure=running`

4. Add `{"Djava.io.tmpdir=/var/tmp":""}` as the value for the `$java_args` parameter of the `puppet_enterprise::profile::master` class using the PE console.

5. Start the agent service by running:

    `puppet resource service puppet ensure=running`

6. **On the Puppet master**, run `puppet agent -t`.

`JAVA_ARGS` in `/etc/default/pe-puppetserver` should have the same value as before.

Note that whether you use `/tmp` or a different directory, you’ll need to set the permissions of the directory to `1777`. This allows the Puppet Server JRuby process to write a file to `/tmp` and then execute it.

## No Config Reload Requests

The Puppet Server service doesn't have a non-disruptive way to request a reload of its configuration files. In order to reload its config, you must restart the service.

Refer to [SERVER-15](https://tickets.puppetlabs.com/browse/SERVER-15).

## HTTPS Client Issues with Newer Apache `mod_ssl` Servers

When configuring the Puppet server to use a report processor that involves HTTPS requests (e.g., to Foreman), there can be compatibility issues between the JVM HTTPS client and certain server HTTPS implementations (e.g., very recent versions of Apache `mod_ssl`). This is usually indicated by a `Could not generate DH keypair` error in Puppet Server's logs. See [SERVER-17](https://tickets.puppetlabs.com/browse/SERVER-17) for known workarounds.

## Puppet Server `max-requests-per-instance` setting leaks JRuby instances in some PE versions

The `max-requests-per-instance` setting for Puppet Server will cause a JRuby process to be replaced once it has served a given number of requests. In PE 2015.2.3 and PE 3.8.3, the outgoing JRuby process will not be available for garbage collection. This causes a memory leak that will tip the server over once enough replacement cycles occur.

This issue does not affect 3.8.2 or 2015.2.2.

A potential workaround:

If `max-active-instances` is not actively mitigating another memory leak, then return the setting to 0 so that new JRubies are not created. If `max-active-instances` is required to mitigate another memory leak, then it should be replaced with a cron job that uses the Puppet Server admin API to flush the entire JRuby pool at regular intervals, as follows:

	curl -X DELETE \
  		--cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem \
  		--cert /etc/puppetlabs/puppet/ssl/certs/pe-internal-classifier.pem \
  		--key /etc/puppetlabs/puppet/ssl/private_keys/pe-internal-classifier.pem \
  	https://<master hostname>:8140/puppet-admin-api/v1/jruby-pool


For Split Installs, this must be done from the `Console` node, or using a different SSL certificate, which is on the `puppet-admin.client-whitelist` in `/etc/puppetlabs/puppetserver/conf.d/pe-puppet-server.conf`.
