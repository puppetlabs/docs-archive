# Puppet Server known issues

These are the known issues for Puppet Server in this PE release.

## File sync error causes `pe-puppetserver` service to shut down due to incorrect `pe-puppet` owner and group permissions

During an r10k deployment, if the `pe-puppet` user does not have permissions to delete files in the environment directory, a file sync error causes the `pe-puppetserver` service to shut down during deployment.

This issue can be fixed by setting permissions in the environment directory to owner `pe-puppet` and group `pe-puppet` with the command: `chown -R pe-puppet:pe-puppet /etc/puppetlabs/code/environments`.

## Installing gems when Puppet Server is behind a proxy requires manual download of gems

If you run Puppet Server behind a proxy, the `puppetserver gem install` command fails. Instead you can install the gems as follows:

1.  Use rubygems.org to search for and download the gem you want to install, and then transfer that gem to your master.
2.  Run `/opt/puppetlabs/bin/puppetserver gem install --local <PATH TO GEM>`.

## Puppet Server run issue when /tmp/ directory mounted noexec

In some cases, especially for RHEL 7 installations, if the `/tmp` directory is mounted as `noexec`, Puppet Server might fail to run correctly, and you might see an error in logs similar like:

```
Nov 12 17:46:12 fqdn.com java[56495]: Failed to load feature test for posix: can't find user for 0
Nov 12 17:46:12 fqdn.com java[56495]: Cannot run on Microsoft Windows without the win32-process, win32-dir and win32-service gems: Win32API only supported on win32
Nov 12 17:46:12 fqdn.com java[56495]: Puppet::Error: Cannot determine basic system flavour
```

To work around this issue, you can either mount the `/tmp` directory without `noexec`, or you can choose a different directory to use as the temporary directory for the Puppet Server process.

If you want to use a directory other than `/tmp`, you can add an extra argument to the `$java_args` parameter of the `puppet_enterprise::profile::master` class using the console, for example, add `{"Djava.io.tmpdir=/var/tmp":""}` as the value for the `$java_args` parameter.

`JAVA_ARGS` in `/etc/default/pe-puppetserver` should have the same value as before.

Whether you use `/tmp` or a different directory, you must set the permissions of the directory to `1777`. This allows the Puppet Server JRuby process to write a file to `/tmp` and then execute it.

## HTTPS client issues with newer Apache mod\_ssl servers

When configuring Puppet Server to use a report processor that involves HTTPS requests, you might encounter compatibility issues between the JVM HTTPS client and certain server HTTPS implementations. This is usually indicated by a `Could not generate DH keypair` error in the Puppet Server logs. See SERVER-17 for workarounds.

