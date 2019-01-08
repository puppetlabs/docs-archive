---
layout: default
title: "Welcome to Puppet Enterprise"
canonical: "/pe/latest/overview_about_pe.html"
---


Thank you for choosing Puppet Enterprise (PE), IT automation software that allows system administrators to programmatically provision, configure, and manage servers, network devices, and storage, in a data center or in the cloud.

This user's guide will help you start using Puppet Enterprise 2016.1, and will serve as a reference as you gain more experience. It covers PE-specific features and offers brief introductions to Puppet and MCollective. Use the **navigation at left** to move between the guide's sections and chapters.

> For new users
> -----
>
> If you've never used PE before and want to evaluate it, you can download PE with 10 complimentary licenses and follow the [Puppet Enterprise quick start guide](./quick_start.html). This walkthrough will guide you through creating a small proof-of-concept deployment while demonstrating the core features and workflows of PE.

> For returning users
> -----
>
> See the [release notes](./release_notes.html) for detailed information about new features in the PE 2016.1 series.

About Puppet Enterprise
-----

Puppet Enterprise makes it easy to automate the provisioning, configuration and ongoing management of your machines and the software running on them. Make rapid, repeatable changes and automatically enforce the consistency of systems and devices – across physical and virtual machines, on premise or in the cloud.

- Reduce cycle times to get more software deployed
- Make fast, iterative changes
- Define a configuration once, and apply it to thousands of machines
- Automatically remediate configuration drift
- Get detailed insight into hardware and software configurations

Puppet Enterprise's Architecture
-----

The [PE architecture overview](./pe_architecture_overview.html) explains the major services and components that comprise PE.


Licensing
-----

PE can be evaluated with a complimentary 10-node license. For larger deployments, you will need a commercial per-node license. A license key file will be emailed to you after your purchase, and the Puppet master will look for this key at `/etc/puppetlabs/license.key`. PE will log warnings if the license is expired or exceeded, and you can view the status of your license by running `puppet license` at the command line on the Puppet master.

To purchase a license, please see the [Puppet Enterprise pricing page](https://puppet.com/product/pricing), or contact Puppet at <sales@puppet.com> or (877) 575-9775. If you have misplaced or never received your license key, please contact <sales@puppet.com>.

#### License info in the PE console

The licenses menu in the PE console shows you how many nodes are currently active and how many are still available on your current license. If the number of available licenses is exceeded, it will display a warning. The licenses menu also provides convenient links to purchase and pricing information.

The console will cache license information for some time, so if you have made changes to your license file (e.g. adding or renewing licenses), the changes may not show for up to 24 hours.

#### Node counting

To ensure you aren't paying for decommissioned or inactive nodes, the number of used licenses is determined by the number of active nodes known to PuppetDB.

Unused nodes are deactivated automatically after seven days with no activity (no new facts, catalogs, or reports), or you can use `puppet node deactivate` (on the Puppet master server) for immediate results.


* * *

- [Next: Release notes](./release_notes.html)
