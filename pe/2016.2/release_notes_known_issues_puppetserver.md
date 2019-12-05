---
layout: default
title: "Puppet Server known issues"
canonical: "/pe/latest/release_notes_known_issues_puppetserver.html"
---

This page lists known issues for Puppet Server in Puppet Enterprise.

## Puppet Server release notes

This version of PE includes Puppet Server 2.4.0. Refer to the [Puppet Server release notes]({{puppetserver}}/release_notes.html) for more information.

## File sync error causes `pe-puppetserver` service to shut down due to incorrect `pe-puppet` owner and group permissions

During an r10k deployment, if the `pe-puppet` user does not have permissions to delete files in the environment directory, a file sync error will cause the `pe-puppetserver` service to shut down during deployment.

This issue can be fixed by setting permissions in the environment directory to owner `pe-puppet` and group `pe-puppet` with the following command: `chown -R pe-puppet:pe-puppet /etc/puppetlabs/code/environments`.


## Puppet Server developments impact custom certificate extensions

A certificate generated with custom extensions in PE 2015.2 might not be properly decodable if used in a prior PE release.

## Updating Puppet master gems

If you've installed any additional Ruby gems beyond those installed by PE, they will not be migrated to the Puppet master during the upgrade to PE 3.8. If you have modules that depend on additional gems, you will need to install them on the Puppet master after you complete the upgrade process.

You can update the gems used by your Puppet master with `/opt/puppetlabs/bin/puppetserver gem install <GEM NAME>`.

After updating the gems, you need to restart the Puppet master with `service pe-puppetserver restart`. You should do this **before** doing any Puppet agent runs.

>**Note**: Be sure you run as `root` when installing `puppetserver` gems.

## Installing gems when Puppet Server is behind a proxy requires manual download of gems

If you run Puppet Server behind a proxy, the `puppetserver gem install` command will fail. Instead you can install the gems as follows:

1. Use [rubygems.org](https://rubygems.org/pages/download#formats) to search for and download the gem you want to install, and then transfer that gem to your Puppet master.
2. Run `/opt/puppetlabs/bin/puppetserver gem install --local <PATH to GEM>`.

## A note about gems with native (C) extensions for JRuby on the Puppet Server

Please see the Puppet Server documentation for a description of this issue, [Gems with native (C) extensions]({{puppetserver}}/gems.html#gems-with-native-c-extensions).

## Puppet Server run issue when `/tmp/` directory mounted `noexec`

In some cases (especially for RHEL 7 installations) if the `/tmp` directory is mounted as `noexec`, Puppet Server might fail to run correctly, and you might see an error in the Puppet Server logs similar to the following:

    Nov 12 17:46:12 fqdn.com java[56495]: Failed to load feature test for posix: can't find user for 0
    Nov 12 17:46:12 fqdn.com java[56495]: Cannot run on Microsoft Windows without the win32-process, win32-dir and win32-service gems: Win32API only supported on win32
    Nov 12 17:46:12 fqdn.com java[56495]: Puppet::Error: Cannot determine basic system flavour

To work around this issue, you can either mount the `/tmp` directory without `noexec`, or you can choose a different directory to use as the temporary directory for the Puppet Server process.

If you want to use a directory other than `/tmp`, you can add an extra argument to the `$java_args` parameter of the `puppet_enterprise::profile::master` class using the PE console. Add `{"Djava.io.tmpdir=/var/tmp":""}` as the value for the `$java_args` parameter. Refer to [Editing parameters](./console_classes_groups_making_changes.html#editing-parameters) for instructions on editing parameters in the console.

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

## No config reload requests

The Puppet Server service doesn't have a non-disruptive way to request a reload of its configuration files. In order to reload its config, you must restart the service.

Refer to [SERVER-15](https://tickets.puppetlabs.com/browse/SERVER-15).

## HTTPS client issues with newer Apache `mod_ssl` servers

When configuring the Puppet server to use a report processor that involves HTTPS requests (e.g., to Foreman), there can be compatibility issues between the JVM HTTPS client and certain server HTTPS implementations (e.g., very recent versions of Apache `mod_ssl`). This is usually indicated by a `Could not generate DH keypair` error in Puppet Server's logs. See [SERVER-17](https://tickets.puppetlabs.com/browse/SERVER-17) for known workarounds.
