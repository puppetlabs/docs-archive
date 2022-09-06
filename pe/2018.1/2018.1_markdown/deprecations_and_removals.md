# Deprecations and removals

These are the features and functions deprecated or removed from PE 2018.1.x.

## Application orchestration \(2018.1.16\)

Application orchestration and these corresponding features of the Puppet language are deprecated:

-   Keywords: `site`, `application`, `consumes`, and `produces`
-   Metaparameters: `export` and `consume`
-   Resource kinds: `application`, `site`, `capability_mapping`
-   `Puppet::Parser::EnvironmentCompiler`
-   `Puppet::Parser::Compiler::CatalogValidator::SiteValidator`

    `Puppet::Parser::Compiler::CatalogValidator::EnvironmentRelationshipValidator`

-   `Puppet::Type#is_capability?`

    `Puppet::Type#application?`

-   Environment catalog REST API

## Split and large environment installations \(2018.1.8\)

The split and large environment installations, where the master, console, and PuppetDB were installed on separate nodes, are no longer recommended. Because compile masters do most of the intensive computing, installing the console and PuppetDB on separate nodes doesn't substantially improve load capability, and adds unnecessary complexity.

For new installations, we now recommend only monolithic configurations, where the infrastructure components are installed on the master. You can add one or more compile masters and a load balancer to this configuration to expand capacity up to 20,000 nodes, and for even larger installations, you can install standalone PE-PostgreSQL on a separate node. For details about current installation configurations, see [Choosing an architecture](choosing_an_architecture.md). For instructions on migrating from a split installation to a monolithic installation, see [Migrate from a split to a monolithic installation](upgrading_pe.md#).

## Platforms reaching end of support \(2018.1.8\)

These platforms have reached end-of-life and are no longer supported.

**Agent**

-   Enterprise Linux 6 and 7 IBM z System

-   SUSE Linux Enterprise Server 11 and 12 IBM z System


**Network device agent**

-   Cumulus Linux


## Developer dashboard \(2018.1.7\)

The developer dashboard was removed in Puppet Server version 5.3.7.

## Platforms reaching end of support \(2018.1.7\)

These platforms have reached end-of-life and are no longer supported.

**Agent**

-   Fedora 27


## External PostgreSQL option in web-based installation \(2018.1.5\)

The option to use an external PostgreSQL instance has been removed from the web-based installer. To install with external PostgreSQL, you must use the text-based installation method.

## Platforms reaching end of support \(2018.1.5\)

These platforms have reached end-of-life and are no longer supported.

**Agent**

-   Fedora 26


## Puppet Enterprise 2018.1 support for MCollective

Puppet Enterprise 2018.1 is the last release to support Marionette Collective, also known as MCollective. While PE 2018.1 remains supported, Puppet will continue to address security issues for MCollective. Feature development has been discontinued. Future releases of PE will not include MCollective. For more information, see the [Puppet Enterprise support lifecycle](https://puppet.com/misc/puppet-enterprise-lifecycle).

To prepare for these changes, migrate your MCollective work to Puppet orchestrator to automate tasks and create consistent, repeatable administrative processes. Use orchestrator to automate your workflows and take advantage of its integration with Puppet Enterprise console and commands, APIs, role-based access control, and event tracking.



## Select database user settings \(2018.1.0\)

These parameters are deprecated and will be removed in a future version of PE:

-   `puppet_enterprise::activity_database_user`
-   `puppet_enterprise::classifier_database_user`
-   `puppet_enterprise::orchestrator_database_user`
-   `puppet_enterprise::rbac_database_user`

## Platforms reaching end of support \(2018.1.0\)

These platforms have reached end-of-life and are no longer supported.

**Master**

-   SUSE Linux Enterprise Server 11

-   Ubuntu 14.04


**Agent**

-   Debian 7

-   Fedora 25

-   macOS 10.10 and 10.11

-   Scientific Linux 5

-   Windows Vista


