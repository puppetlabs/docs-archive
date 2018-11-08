---
layout: default
title: "PE 3.8 » Security and SSL » Custom Diffie-Hellman Param File Generation"
subtitle: "Configuring the PE Console to Use a Custom Diffie-Hellman File"
canonical: "/pe/latest/trouble_dh_generate.html"
---

The "Logjam Attack" ([CVE-2015-4000](https://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2015-4000)) has exposed several weaknesses in Diffie-Hellman (DH) key exchange, a commonly used method for encrypting and negotiating secure Internet connections via shared keys. Diffie-Hellman key exchange is used by many Internet protocols, including HTTPS, SSH, and IPsec, SMTPS. For reference, see [https://weakdh.org](https://weakdh.org/).

To help mitigate the "Logjam Attack," PE 3.8.1 ships with a pre-generated 2048 bit Diffie-Hellman param file. 

In the case that you don't want to use the PE default DH param file, the following procedure shows how to generate your own.

**To generate a custom DH param file**:

> **Note**: In the following procedure, `<DASHBOARD-CUSTOM-dhparam>.pem` and `<PROXY-CUSTOM-dhparam>.pem` can be replaced with any file names, except `dhparam_puppetdashboard.pem` and `dhparam_puppetproxy.pem`, as these are the default files names used by PE. 

1. **On your PE console server**, (for a mono install, this is the same node as a the Puppet master), run the following commands:

   ~~~
   /opt/puppet/bin/openssl dhparam -out /opt/puppet/share/puppet-dashboard/certs/<DASHBOARD-CUSTOM-dhparam>.pem 2048
   /opt/puppet/bin/openssl dhparam -out /opt/puppet/share/puppet-dashboard/certs/<PROXY-CUSTOM-dhparam>.pem 2048
   ~~~
   
   >**Note**: This step may take several moments to complete.

2. **On your Puppet master**, edit your Hiera defaults file (the default location is `/var/lib/hiera/defaults.yaml`) so that it contains the following:

   ~~~
   puppet_enterprise::console::config::dhparam_file: /opt/puppet/share/puppet-dashboard/certs/<DASHBOARD-CUSTOM-dhparam>.pem
   puppet_enterprise::profile::console::apache_proxy::dhparam_file:/opt/puppet/share/puppet-dashboard/certs/<PROXY-CUSTOM-dhparam>.pem
   ~~~
   
3. **On your PE console server**, kick off a Puppet run with `puppet agent -t`.