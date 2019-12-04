# Disable TLSv1 in PE

You can disable TLSv1 in PE to comply with standards as necessary.

## About this task

The services running in PE support versions 1, 1.1, and 1.2 of the Transport layer security \(TLS\) protocol but use TLSv1 by default. The Payment Card Industry Data Security Standard \(PCI DSS\) requires TLSv1 to be permanently disabled by 30 June, 2018. To comply with PCI DSS, or simply to tighten your own security, disable TLSv1.

PE uses TLSv1 by default because the PXP agent service running on older agents use TLSv1. In PE you can disable TLSv1, but the first step is upgrading your agents to 2017.2 or later.

**Note:** AIX supports only TLSv1. If you disable TLSv1, install AIX agents with your own package management instead of PE package management.

## Procedure

1.  Upgrade your \*nix or Windows agents to the latest version of PE \(must be 2017.2 or later\).

2.  In the console, click **Classification** \> **PE Infrastructure**

3.  On the **Configuration** tab, add the following parameter and value:

    |Parameter|Value|
    |---------|-----|
    |`puppet_enterprise::ssl_protocols`|`["TLSv1.1", "TLSv1.2"]`|

4.  Click **Add parameter**, and commit changes.

5.  Run Puppet on the master.


