---
layout: default
title: "Use a custom SSL certificate for the PE console"
canonical: "/pe/latest/custom_console_cert.html"
---

The PE console uses a certificate signed by PE's built-in certificate authority (CA). Since this CA is specific to PE, web browsers don't know it or trust it, and you have to add a security exception in order to access the console. You may find that this is not an acceptable scenario and want to use a custom CA to create the console's certificate. 

**Before you begin:**

You should have a X.509 cert, signed by the custom party CA, in PEM format, with matching private and public keys.

If your custom cert is issued by an intermediate CA, the CA bundle needs to contain a complete chain, including the applicable root CA.

>**Note**: The following procedure is only compatible with PE 2015.2 or higher. 

1. Retrieve the custom certificate's public and private keys, and, for ease of use, name them as follows:

   * `public-console.cert.pem`
   * `public-console.private_key.pem`

   > **Note**: These keys and certs must be in PEM format. You will need to convert them to PEM format before proceeding.

2. Add the files from step 1 to `/opt/puppetlabs/server/data/console-services/certs/`. (Note that if you have a split install, this directory is on the PE console node.)
3. Use the console to edit the parameters of the `puppet_enterprise::profile::console` class.

   a. Click **Classification**, and in the **PE Infrastructure** group, select the **PE Console** group.

   b. On the **Classes** tab, in the `puppet_enterprise::profile::console` class, add these parameters:

      - `browser_ssl_cert` = `/opt/puppetlabs/server/data/console-services/certs/public-console.cert.pem`
      - `browser_ssl_private_key` = `/opt/puppetlabs/server/data/console-services/certs/public-console.private_key.pem`

   c. Commit changes.

4. Kick off a Puppet run. (If you have a split install, the Puppet run needs to happen on the PE console node.)

You should now be able to navigate to your console and see the custom certificate in your browser.
