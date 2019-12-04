# Deprecations and removals

These are the features and functions deprecated or removed from PE 2019.0.

## Console admin password reset script \(2019.0.2\)

The Ruby script for resetting the console adminstrator password has been deprecated in favor of the new ``puppet infra console_password` command`. 

## Puppet Enterprise 2019.0 support for Application Orchestration \(2019.0.2\)

Puppet Enterprise 2019.0 is the last short term support \(STS\) release to support Application Orchestration. While PE 2019.0 remains supported, Puppet will continue to address security issues for Application Orchestration. Feature development has been discontinued. Future releases of PE will not include Application Orchestration. For more information, see the [Puppet Enterprise support lifecycle](https://puppet.com/misc/puppet-enterprise-lifecycle).

## Platforms reaching end of support \(2019.0.2\)

These platforms have reached end-of-life and are no longer supported.

**Agent**

-   Fedora 27


## MCollective \(2019.0.0\)

MCollective and ActiveMQ have been removed from PE after being deprecated in the previous version, 2018.1. If you're upgrading from a 2018.1 installation with MCollective enabled, you must take additional steps to ensure a successful upgrade. For details, see [Upgrade cautions](upgrading_pe.md#). 

## Split and large environment installations \(2019.0.0\)

The split and large environment installations, where the master, console, and PuppetDB were installed on separate nodes, are no longer recommended. Because compile masters do most of the intensive computing, installing the console and PuppetDB on separate nodes doesn't substantially improve load capability, and adds unnecessary complexity.

For new installations, we now recommend only monolithic configurations, where the infrastructure components are installed on the master. You can add one or more compile masters and a load balancer to this configuration to expand capacity up to 20,000 nodes, and for even larger installations, you can install standalone PE-PostgreSQL on a separate node. For details about current installation configurations, see [Choosing an architecture](choosing_an_architecture.md). For instructions on migrating from a split installation to a monolithic installation, see [Migrate from a split to a monolithic installation](upgrading_pe.md#).

## External PostgreSQL option in web-based installation \(2019.0.0\)

The option to use an external PostgreSQL instance has been removed from the web-based installer. To install with external PostgreSQL, you must use the [text-based installation](installing_pe.md#) method.

## Network device agents \(2019.0.0\)

This version removes support for network device agents in favor of agentless device management. Agentless devices are managed with a combination of tasks and the Puppet device framework used, for example, in the [Cisco iOS module](https://puppet.com/blog/introducing-new-cisco-ios-module-agentless-device-management). Agentless device management enables you to manage a wider range of devices with significantly lower overhead. Both the [ciscopuppet](https://forge.puppet.com/puppetlabs/ciscopuppet) and [cisco\_ios](https://forge.puppet.com/puppetlabs/cisco_ios) modules will be supported agentlessly for 2019.0.0 onwards.

For information on how to transition from module 1.10 and agent-based management, to module 2.0 and agentless management, see [Transitioning to latest module version](https://github.com/cisco/cisco-network-puppet-module/blob/develop/docs/README-install_guide.md#transitioning-to-latest-module-version).

Network device agents continue to be supported in PE 2018.1, available through 2020.

## `aix_61_power` and `aix_71_power` classes \(2019.0.0\)

The `aix_61_power` and `aix_71_power` classes have been deprecated in favor of `aix_power`. If you have AIX agents, replace the deprecated classes before upgrading to this version of PE to avoid encountering a duplicate declaration error.

## Platforms reaching end of support \(2019.0.0\)

These platforms have reached end-of-life and are no longer supported.

**Agent**

-   Fedora 26

-   IBM Z Systems \(s390x\) on all platforms, including Enterprise Linux 6 and 7 and SUSE Linux Enterprise Server 11 and 12


