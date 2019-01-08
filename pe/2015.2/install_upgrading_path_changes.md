---
layout: default
title: "PE 2015.2 » Installing » Upgrading (Major Path Changes)"
subtitle: "Major Path Changes in PE 2015.2"
canonical: "/pe/latest/install_upgrading_path_changes.html"
---

[confdir]: /puppet/4.2/reference/dirs_confdir.html
[directory environments]: /puppet/4.2/reference/environments.html
[main manifest]: /puppet/4.2/reference/dirs_manifest.html

In PE 2015.2 (and Puppet 4.2), we changed the locations of several important configuration files and directories. We also changed Puppet's packaging to install different things in different places.

This page is a summary to quickly orient users arriving from PE 3.8.x.

## *nix Executables moved to `/opt/puppetlabs/bin/`

On \*nix platforms, the main executables moved to `/opt/puppetlabs/bin`. This means **Puppet and related tools aren't included in your PATH by default.** You'll need to either:

* Add `/opt/puppetlabs/bin` to your PATH.
* Symlink Puppet's tools to somewhere else in your PATH.
* Use the full path (e.g., `/opt/puppetlabs/bin/puppet apply`) when running Puppet commands.

On Windows, executables stayed in the same location, and the MSI package still adds Puppet's tools to your PATH.

### Private `bin` Directories

The executables in `/opt/puppetlabs/bin` are just the "public" applications that make up Puppet. Private supporting commands like `ruby` and `gem` are in `/opt/puppetlabs/puppet/bin` to keep them from accidentally overriding system tools if you add the public bin directory to your PATH.

## \*nix `confdir` moved to `/etc/puppetlabs/puppet`

Puppet's system [`confdir`][confdir] (used by `root` and the `puppet` user) is now `/etc/puppetlabs/puppet`. Open source Puppet now uses the same `confdir` as Puppet Enterprise.

The `confdir` directory contains config files such as `puppet.conf` and `auth.conf`, as well as the SSL directory.

On Windows, `confdir` stayed the same. On modern Windows versions (Windows 7 and newer or Windows Server 2008 and newer), it's still in the `COMMON_APPDATA` folder, defaulting to `C:\ProgramData\PuppetLabs\puppet\etc`.

### Other Stuff in `/etc/puppetlabs`

We're also moving other related configs into the `/etc/puppetlabs` directory. Puppet Server now uses `/etc/puppetlabs/puppetserver`, and MCollective uses `/etc/puppetlabs/mcollective`. In addition, in `/etc/puppetlabs`, you'll find the `console-services`, `r10k`, `nginx`, `installer`, and `activemq` directories.

## New `codedir` Holds All Modules/Manifests/Data

All of the content used to configure nodes has moved into a new directory, named `codedir`. (This stuff used to be in the `confdir`.)

>**Note**: On upgrades we do not move any of your code configurations to this new directory. It'll stay configured as you had it in the previous version of PE.

The default `codedir` location is:

* `/etc/puppetlabs/code` on \*nix.
* `C:\ProgramData\PuppetLabs\code` on Windows.
* `<USER DIRECTORY>/.puppetlabs/etc/code` if you're running as a non-root user.

The main contents of `codedir` are:

* The `environments` directory
* The global `modules` directory
    * Note: This is only for module versions you specifically want in _all_ environments.
* The `hiera.yaml` config file
* The `hieradata` directory

### Directory Environments are Always On

[Directory environments][] are always enabled now.

The default `environmentpath` is `$codedir/environments`. On a fresh install, we create a directory for the default `production` environment.

This means that if you're starting from scratch, you should:

* Put modules in `$codedir/environments/production/modules`.
* Put your [main manifest][] in `$codedir/environments/production/manifests`.

You can still put global modules in `$codedir/modules` and configure a global [main manifest][] with the `default_manifest` setting.

## Hiera Path Changes

- The default path for `hiera.yaml` has moved to `/etc/puppetlabs/code/hiera.yaml`.
- The default Hiera datadir (which is used if you specify no other datadir) has moved. The following are the new paths:

   - *nix: `/etc/puppetlabs/code/environments/%{environment}/hieradata`
   - *windows: `C:\ProgramData\PuppetLabs\code\environments\%{environment}\hieradata`

- Hiera defaults are now in the datadir setting in `hiera.yaml`.
- The datadir (`/etc/puppetlabs/code/environments/%{environment}/hieradata`) contains `common.yaml` and the nodes directory.

If you are upgrading to PE 2015.2 and already have a `hiera.yaml` file, that file persists after the upgrade, and the paths used in it also persist---in other words, we do not touch this file on upgrades. However a copy of `hiera.yaml` is copied to the new default path (`/etc/puppetlabs/code/hiera.yaml`).

In addition, `hiera.yaml` is automatically configured for use after upgrading.

## Miscellaneous Directory Changes

* The system `vardir` for `puppet agent` has moved, and is now separate from Puppet Server's `vardir`.
    * On \*nix: `/opt/puppetlabs/puppet/cache`.
    * On Windows: `C:\ProgramData\PuppetLabs\puppet\cache`.
* The `rundir`, where the service PID files go, has moved:
    * On \*nix: `/var/run/puppetlabs`. (Puppet Server has a `puppetserver` directory in this directory.)
    * On Windows: `C:\ProgramData\PuppetLabs\puppet\var\run` --- this is the same as before, but it's now outside the main `vardir`.
