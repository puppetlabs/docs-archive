---
layout: default
title: "Puppet Enterprise Overview"
canonical: "/pe/latest/overview_about_pe.html"
---


Thank you for choosing Puppet Enterprise (PE), IT automation software that allows system administrators to programmatically provision, configure, and manage servers, network devices, and storage, in a data center or in the cloud.

This user's guide will help you start using Puppet Enterprise 2015.3, and will serve as a reference as you gain more experience. It covers PE-specific features and offers brief introductions to Puppet and MCollective. Use the **navigation at left** to move between the guide's sections and chapters.

> For New Users
> -----
>
> If you've never used PE before and want to evaluate it, you can download PE with ten complimentary licenses and follow the [Puppet Enterprise quick start guide](./quick_start.html). This walkthrough will guide you through creating a small proof-of-concept deployment while demonstrating the core features and workflows of PE.

> For Returning Users
> -----
>
> See the [release notes](./release_notes.html) for detailed information about new features in the PE 2015.3 series.

About Puppet Enterprise
-----

Puppet Enterprise is a comprehensive tool for managing the configuration of enterprise systems. Specifically, PE offers:

* Configuration management tools that let you define a desired state for your infrastructure and then automatically enforce that state.
* A web-based console UI and APIs for analyzing events, managing your nodes and users, and editing resources on the fly.
* Powerful orchestration capabilities.
* An advanced provisioning application called Razor that can deploy bare metal systems.

PE consists of a complete stack of Puppet Labs' technologies, which are automatically installed and connected. [What Gets Installed Where](./install_what_and_where.html#puppet-enterprise-software-components) includes a list of all the major packages that comprise PE 2015.3.

About Puppet
-----

Puppet is the leading open source configuration management tool. It allows system configuration "manifests" to be written in a high-level <abbr title="Domain-Specific Language">DSL</abbr> and can compose modular chunks of configuration to create a machine's unique configuration. PE uses a client/server Puppet deployment, where agent nodes fetch configurations from a central Puppet master.

Puppet Enterprise Architecture
-----

The [PE architecture overview](./pe_architecture_overview.html) explains the major services and components that comprise PE.

Licensing
-----

Puppet Enterprise can be evaluated with a complimentary ten-node license; beyond that, a commercial per-node license is required for use. A license key file is emailed to you after your purchase. On a monolithic installation, the Puppet master looks for this key at `/etc/puppetlabs/license.key`. On a split installation, it looks for this key at `/etc/puppetlabs/license.key` on the Puppet master and the PE console nodes. Puppet will log warnings if the license is expired or exceeded. You can view the status of your license by running `puppet license` at the command line on the Puppet master.

To purchase a license, please see the [PE pricing page](http://www.puppetlabs.com/puppet/how-to-buy/), or contact Puppet Labs at <sales@puppetlabs.com> or (877) 575-9775. For more information on licensing terms, please see [the licensing FAQ](http://www.puppetlabs.com/licensing-faq/). If you have misplaced or never received your license key, please contact <sales@puppetlabs.com>.

#### License Info in the PE Console

The licenses menu in the PE console shows you how many nodes are currently active and how many are still available on your current license. If the number of available licenses is exceeded, it will display a warning. The licenses menu also provides convenient links to purchase and pricing information.

The console will cache license information for some time, so if you have made changes to your license file (e.g. adding or renewing licenses), the changes may not show for up to 24 hours.

#### Node Counting

To ensure you aren't paying for decommissioned or inactive nodes, the number of used licenses is determined by the number of active nodes known to PuppetDB.

Unused nodes are deactivated automatically after seven days with no activity (no new facts, catalogs, or reports), or you can use `puppet node deactivate` (on the Puppet master server) for immediate results.


* * *

- [Next: Release Notes](./release_notes.html)
