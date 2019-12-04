# Use a custom SSL certificate for the console

The console uses a certificate signed by PE's built-in certificate authority \(CA\). Since this CA is specific to PE, web browsers don't know it or trust it, and you have to add a security exception in order to access the console. You may find that this is not an acceptable scenario and want to use a custom CA to create the console's certificate.

## Before you begin

-   You should have a X.509 cert, signed by the custom party CA, in PEM format, with matching private and public keys.
-   If your custom cert is issued by an intermediate CA, the CA bundle needs to contain a complete chain, including the applicable root CA.
-   The keys and certs used in this procedure must be in PEM format.

## Procedure

1.  Retrieve the custom certificate's public and private keys, and, for ease of use, name them as follows:

    -   public-console.cert.pem
    -   public-console.private\_key.pem
2.  Add the files from step 1 to `/opt/puppetlabs/server/data/console-services/certs/`.

    If you have a split install, this directory is on the console node.

3.  Use the console to edit the parameters of the `puppet_enterprise::profile::console` class.

    1.  Click **Classification**, and in the **PE Infrastructure** group, select the **PE Console** group.

    2.  On the **Configuration** tab, in the `puppet_enterprise::profile::console` class, add the following parameters:

        |Parameter|Value|
        |---------|-----|
        |`browser_ssl_cert`|`/opt/puppetlabs/server/data/console-services/certs/public-console.cert.pem`|
        |`browser_ssl_private_key`|`/opt/puppetlabs/server/data/console-services/certs/public-console.private_key.pem`|

    3.  Commit changes.

4.  Run Puppet.

    If you have a split install, the Puppet run needs to happen on the PE console node.


## Results

You should now be able to navigate to your console and see the custom certificate in your browser.

