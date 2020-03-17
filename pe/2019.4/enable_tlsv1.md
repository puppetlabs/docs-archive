# Enable TLSv1

TLSv1 and TLSv1.1 are disabled by default in PE.

You must enable TLSv1 to install agents on these platforms:

-   AIX

-   CentOS 5

-   RHEL 5

-   SLES 11

-   Solaris 10

-   Windows Server 2008r2


To comply with security regulations, PE 2019.1 and later uses only version 1.2 of the Transport Layer Security \(TLS\) protocol.

1.  In the console, click **Classification** \> **PE Infrastructure**.

2.  On the **Configuration** tab, in the **Data** area, add the following class, parameter, and value:

    |Class|Parameter|Value|
    |-----|---------|-----|
    |`puppet_enterprise::master::puppetserver`|`ssl_protocols`|`["TLSv1", "TLSv1.1", "TLSv1.2"]`|

3.  Click **Add data**, and commit changes.

4.  Run Puppet on the master.


**Parent topic:**[SSL and certificates](ssl_and_certificates.md)

