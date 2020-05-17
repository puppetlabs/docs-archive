---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# PE and FIPS compliance

Puppet Enterprise \(PE\) complies with Federal Information Processing Standard \(FIPS\) 140-2 standards and is fully operable on select FIPS platforms.

Federal Information Processing Standard \(FIPS\) 140-2 is a standard defined by the U.S. government that defines the security requirements for cryptographic modules. The standard is published and maintained by the National Institute of Standards and Technology \(NIST\) and is mandated by the White House Office of Management and Budget \(OMB\) in [Circular A-130](https://www.whitehouse.gov/sites/whitehouse.gov/files/omb/circulars/A130/a130revised.pdf) and pursuant to FISMA \(44 U.S.C Chapter 35\). NIST operates the [Cryptographic Module Validation Program \(CMVP\)](https://csrc.nist.gov/Projects/Cryptographic-Module-Validation-Program), which validates cryptographic modules per the requirements defined by the standard. All federal information systems that transmit and store sensitive information must utilize FIPS 140-2-validated cryptography.

The cryptographic modules included in Puppet Enterprise are compliant with FIPS 140-2 and fully operable on the FIPS-compliant platforms listed below.

|PE component|FIPS-compliant platforms|
|------------|------------------------|
|Master|Red Hat Enterprise Linux \(RHEL\) 7 in FIPS mode|
|Agents|Red Hat Enterprise Linux \(RHEL\) 7 in FIPS modeWindows Server 2012 R2 and newer versions in FIPS mode

Windows 10 in FIPS mode

|

For information on installing a FIPS-compliant PE master and agents, see [Installing FIPS-compliant Puppet Enterprise](installing_fips_pe.md).

**Important:** Upgrading from non-FIPS-compliant versions of PE to FIPS-compliant PE is not supported.

In order to operate on FIPS platforms in compliance mode, PE includes the following changes:

-   All components are built and packaged against system OpenSSL \(Red Hat Enterprise Linux \(RHEL\) 7 in FIPS mod for the master\), or against OpenSSL built in FIPS mode \(Windows 10, Windows Server 2012 R2 and newer versions for agents\).
-   All use of MD5 hashes for security has been eliminated and replaced.
-   Forge and module tooling now use SHA-256 hashes to verify the identity of modules.
-   Proper random number generation devices are used on all platforms.
-   All Java and Clojure components use FIPS Bouncy Castle encryption providers on FIPS platforms.

