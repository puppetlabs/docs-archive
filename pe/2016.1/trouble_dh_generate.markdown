---
layout: default
title: "Custom Diffie-Hellman param file generation"
canonical: "/pe/latest/trouble_dh_generate.html"
---

The "Logjam Attack" ([CVE-2015-4000](https://web.nvd.nist.gov/view/vuln/detail?vulnId=CVE-2015-4000)) exposed several weaknesses in Diffie-Hellman (DH) key exchange, a commonly used method for encrypting and negotiating secure Internet connections via shared keys. Diffie-Hellman key exchange is used by many Internet protocols, including HTTPS, SSH, and IPsec, SMTPS. For reference, see [https://weakdh.org](https://weakdh.org/).

To help mitigate the "Logjam Attack," PE ships with a pre-generated 2048 bit Diffie-Hellman param file.

In the case that you don't want to use the PE default DH param file, the following procedure shows how to generate your own.

**To generate a custom DH param file**:

> **Note**: In the following procedure, `<PROXY-CUSTOM-dhparam>.pem` can be replaced with any file name, except `dhparam_puppetproxy.pem`, as this is the default file name used by PE.

1. **On your PE console server**, (for a mono install, this is the same node as a the Puppet master), run the following command:

   ~~~
   /opt/puppetlabs/puppet/bin/openssl dhparam -out /etc/puppetlabs/nginx/<PROXY-CUSTOM-dhparam>.pem 2048
   ~~~

   >**Note**: This step may take several moments to complete.

2. **On your Puppet master**, edit your Hiera defaults file (the default location is `/etc/puppetlabs/code/environments/%{environment}/hieradata`) so that it contains the following:

   ~~~
   puppet_enterprise::profile::console::proxy::dhparam_file: /etc/puppetlabs/nginx/<PROXY-CUSTOM-dhparam>.pem
   ~~~

3. **On your PE console server**, kick off a Puppet run with `puppet agent -t`.