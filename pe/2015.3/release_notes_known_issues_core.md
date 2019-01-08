---
layout: default
title: "PE Services and Puppet Core Known Issues"
canonical: "/pe/latest/release_notes_known_issues_core.html"
---

As we discover them, this page will be updated with known issues related to PE services and the Puppet core in Puppet Enterprise. Fixed issues will be removed from this page and noted in the Bug Fixes section of the release notes. If you find new problems yourself, please file bugs in [our issue tracker](https://tickets.puppetlabs.com).

## New Reserved Words: `application`, `consumes`, and `produces`

Due to changes in the Puppet DSL for Application Orchestration, the following words have been reserved in the Puppet language:

- `application`
- `consumes`
- `produces`

Like all [reserved words](/puppet/4.3/reference/lang_reserved.html), you can’t use these as unquoted strings or as names for classes, defined types, resource types, or custom functions.

If enable Application Orchestration and use these words, you'll encounter a parser error.

## `parser=future` Setting in `environment.conf` Not Valid in PE 2015.3

If you enabled the Puppet 4 language parser in PE 3.8.x by setting `parser=future` in any `environment.conf` files, you'll see warning messages during your upgrade, as this setting is no longer valid. After you upgrade, remove this setting from any `environment.conf` files.

## Remove `enable_future_parser` Parameter After Upgrading

If you enabled the Puppet 4 language parser in PE 3.8 via the console, Hiera, or a third-party classification tool, **after upgrading** you must [use the console to remove](./console_classes_groups_making_changes.html#deleting-parameters) the `enable_future_parser` paramater from the `puppet_enterprise::profile::master` class, as this parameter is deprecated.

## `/opt/staging/` is No Longer Used

In PE 2015.3, the `/opt/staging/` directory is no longer used. Because users may have used either the `puppetlabs-pe_staging` or `nanliu-staging` modules, we did not delete the directory. If you are not using the directory, it is safe to delete it.

## Change to `lsbmajdistrelease` Fact Affects Some Manifests

In Facter 2.2.0, the `lsbmajdistrelease` fact changed its value from the first two numbers to the full two-number.two-number version on Ubuntu systems. This might break manifests that were based on the previous behavior. For example, this fact changed from: `12` to `12.04`.

This change affects Ubuntu and Amazon Linux. See the [Facter documentation for more information](/facter/2.3/release_notes.html#significant-changes-to-existing-facts)

## Change `allow_no_actionpolicy` Parameter to Enforce MCollective Action Policies

The MCollective ActionPolicy plugin is installed by default in PE. Within the configuration of MCollective, there is a setting that can be used to enforce the use of this ActionPolicy. By default this setting (`plugin.actionpolicy.allow_unconfigured`) is hardcoded to `1`. Unfortunately this prevents you from enforcing the use of configured Action Policies.

To change this setting, use the PE console to edit the value of the `allow_no_actionpolicy` parameter of the `puppet_enterprise::profile::mcollective::agent` class located in the PE MCollective node group. To allow ActionPolicy, enter `"0"`. (Be sure to use quote marks, as Puppet expects a string for this value.)

## Enabling NIO and Stomp for ActiveMQ Performance Improvements will Introduce Security Issues

Enabling ActiveMQ's use of the NIO protocol in PE can improve the speed at which messages are sent across your deployment. However, when this is enabled, any parameters that you define for which SSL protocols to use will be ignored, and SSL version 3 will be enabled. Apache has fixed this bug, but they have not yet released a version of ActiveMQ that contains the fix. For more information, refer to their [public ticket](https://issues.apache.org/jira/browse/AMQ-5407).

Considering security over performance, PE 2015.3 ships with NIO disabled. You can enable it with the following procedure:

1. From the console, click __Classification__ in the navigation bar.
2. From the __Classification page__, click the __PE ActiveMQ Broker__ group.
3. Click the __Classes__ tab, and find `puppet_enterprise::profile::amq::broker` in the list of classes.
4. From the __parameter__ drop-down menu, choose `openwire_protocol`, and in the __value__ field add __nio+ssl__.
6. Click __Add parameter__.
7. From the __parameter__ drop-down menu, choose `stomp_protocol`, and in the __value__ field add __stomp+nio+ssl__.
8. Click __Add parameter__.
9. Click __Commit 2 changes__.

## `site.pp` Must Be Duplicated for Each Environment

You can no longer have a universal or global `site.pp`. The default main filebucket is configured as a resource default in `site.pp`. This means that `site.pp` must be duplicated for each environment. See the [Puppet environments documentation for more information](/puppet/4.3/reference/environments.html).

## `puppet module list --tree` Shows Incorrect Dependencies After Uninstalling Modules

If you uninstall a module with `puppet module uninstall <module name>` and then run `puppet module list --tree`, you will get a tree that does not accurately reflect module dependencies.

## The Puppet Module Tool (PMT) Does Not Support Solaris 10

When attempting to use the PMT on Solaris 10, you'll get an error like the following:

		Error: Could not connect via HTTPS to https://forgeapi.puppetlabs.com
  		Unable to verify the SSL certificate
    	The certificate may not be signed by a valid CA
    	The CA bundle included with OpenSSL may not be valid or up to date

This error occurs because there is no CA-cert bundle on Solaris 10 to trust the Puppet Labs Forge certificate. To work around this issue, we recommend that you download directly from the Forge website and then use the Puppet module tool to [install from a local tarball](/puppet/4.3/reference/modules_installing.html#installing-from-a-release-tarball).

