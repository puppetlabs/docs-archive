---
layout: default
title: "Disable TLSV1 in PE"
canonical: "/pe/latest/disable_tlsv1.html"
---

The services running in PE support versions 1, 1.1, and 1.2 of the Transport layer security (TLS) protocol but use TLSv1 by default. The Payment Card Industry Data Security Standard (PCI DSS) requires TLSv1 to be permanently disabled by 30 June, 2018. To comply with PCI DSS, or simply to tighten your own security, disable TLSv1. 

PE uses TLSv1 by default because the pxp-agent service running on older Puppet agents use TLSv1. You can disable TLSv1, but the first step is **upgrading your agents to 2016.4**.

1. Upgrade your [*nix](./install_upgrading_agents.html) or [Windows](./upgrade_windows.html) Puppet agents.
2. In the console, click **Nodes** > **Classification** > **PE Infrastructure**.    
3. On the classes tab, add the following parameter and value:

   Parameter | Value
   ----------|-------
   `puppet_enterprise::ssl_protocols` | `["TLSv1.1", "TLSv1.2"]`

4. Click **Add parameter**, and commit changes.
5. In a monolithic installation, run Puppet on the Puppet master. In a split installation, run Puppet on the Puppet master, PE console, and PuppetDB nodes.

>**Note:** AIX supports only TLSv1. If you disable TLSv1, install AIX agents with [your own package management](./install_agents.html#installing-agents-with-your-own-package-management) instead of PE package management.












