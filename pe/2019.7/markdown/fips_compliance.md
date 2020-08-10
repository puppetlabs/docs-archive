---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# PE and FIPS compliance

Puppet Enterprise \(PE\) complies with Federal Information Processing Standard \(FIPS\) 140-2 standards and is fully operable on select FIPS-compliant platforms.

To install FIPS-compliant PE, simply install your master and agents on a supported platform with FIPS mode enabled. Master and compiler nodes must be configured with sufficient available entropy or the installation process fails.

## Changes in FIPS-compliant installations

In order to operate on FIPS platforms in compliance mode, PE includes the following changes:

-   All components are built and packaged against system OpenSSL for the master, or against OpenSSL built in FIPS mode for agents.
-   All use of MD5 hashes for security has been eliminated and replaced.
-   Forge and module tooling use SHA-256 hashes to verify the identity of modules.
-   Proper random number generation devices are used on all platforms.
-   All Java and Clojure components use FIPS Bouncy Castle encryption providers on FIPS platforms.

## Limitations and cautions for FIPS-compliant installations

Be aware of the following when installing FIPS-compliant PE.

-   Upgrading from non-FIPS-compliant versions of PE to FIPS-compliant PE is not supported.
-   High availability configurations are not supported for FIPS-compliant PE.
-   FIPS-compliant installations don't support extensions that use the standard Ruby Open SSL library, such as heira-eyaml.
-   FIPS-compliant PE does not support Razor, and the `pe-razor-server` package is not included in the installation tarball for FIPS-compliant PE.
-   Due to a known issue with the `pe-client-tools` packages, `puppet code` and `puppet db` commands fail with SSL handshake errors when run on FIPS-enabled hardware. To use `puppet db` commands on a FIPS-enabled machine, install the [puppetdb\_cli](https://rubygems.org/gems/puppetdb_cli) Ruby gem. To use `puppet code` commands on a FIPS-enabled machine, use the [Code Manager API](code_manager_api.md#).

**Related information**  


[Supported operating systems and devices](supported_operating_systems.md#)

