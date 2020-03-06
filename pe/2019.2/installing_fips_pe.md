---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Installing FIPS-compliant Puppet Enterprise

PE version 2019.2 complies with Federal Information Processing Standard \(FIPS\) 140-2 standards. Both the express and text install methods can be used to install a FIPS-compliant PE master on FIPS-compliant platforms.

The cryptographic modules included in Puppet Enterprise version 2019.2 are compliant with FIPS 140-2 and fully operable on the FIPS-compliant platforms listed below.

|PE component|FIPS-compliant platforms|
|------------|------------------------|
|Master|Red Hat Enterprise Linux \(RHEL\) 7 in FIPS mode|
|Agents|Red Hat Enterprise Linux \(RHEL\) 7 in FIPS modeWindows Server 2012 R2 and newer versions in FIPS mode

Windows 10 in FIPS mode

|

For general information about FIPS and more on the updates made to PE to ensure FIPS compliance, see [PE and FIPS compliance](fips_compliance.md).

## Installing a FIPS-compliant PE master

To install a FIPS-compliant PE master, follow the text or express installation instructions to install your PE master on a node with the following characteristics:

-   Running Red Hat Enterprise Linux \(RHEL\) 7 in FIPS mode
-   Configured with good sources of entropy \(see the Red Hat blog post on [Entropy in RHEL](https://developers.redhat.com/blog/2017/10/05/entropy-rhel-based-cloud-instances/) for more information\)

    **Important:** The installation process will fail on a node that lacks sufficient available entropy.


## Installing FIPS-compliant PE agents

To install FIPS-compliant PE agents, follow the agent installation instructions appropriate to your operating system and infrastructure. Your target agent node's operating system must be one of the following:

-   Windows 10 in FIPS mode
-   Windows Server 2012 R2 and newer versions in FIPS mode
-   Red Hat Enterprise Linux \(RHEL\) 7 in FIPS mode

If you wish to classify a FIPS-compliant PE agent as a compiler, the agent must be configured with good sources of entropy \(see the Red Hat blog post on [Entropy in RHEL](https://developers.redhat.com/blog/2017/10/05/entropy-rhel-based-cloud-instances/) for more information\).

## Limitations and cautions for FIPS-compliant installations

Be aware of the following when installing FIPS-compliant PE.

-   Upgrading from non-FIPS-compliant versions of PE to FIPS-compliant PE version 2019.2 is not supported.
-   FIPS-compliant PE version 2019.2 includes PostgreSQL version 9.6.
-   High availability configurations are not supported for FIPS-compliant PE version 2019.2.
-   FIPS-compliant PE does not support Razor, and the `pe-razor-server` package is not included in the installation tarball for FIPS-compliant PE.
-   Due to a known issue with the `pe-client-tools` packages, `puppet code` and `puppet db` commands fail with SSL handshake errors when run on FIPS-enabled hardware. To use `puppet db` commands on a FIPS-enabled machine, install the [puppetdb\_cli](https://rubygems.org/gems/puppetdb_cli) Ruby gem. To use `puppet code` commands on a FIPS-enabled machine, use the [Code Manager API](code_manager_api.md#).

