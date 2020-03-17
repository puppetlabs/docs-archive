# Use a custom SSL certificate for the console

The console uses a certificate signed by PE's built-in certificate authority \(CA\). Because this CA is specific to PE, web browsers don't know it or trust it, and you have to add a security exception in order to access the console. You might find that this is not an acceptable scenario and want to use a custom CA to create the console's certificate.

-   You should have a X.509 cert, signed by the custom party CA, in PEM format, with matching private and public keys.
-   If your custom cert is issued by an intermediate CA, the CA bundle needs to contain a complete chain, including the applicable root CA.
-   The keys and certs used in this procedure must be in PEM format.

1.  Retrieve the custom certificate's public and private keys, and, for ease of use, name them as follows:

    -   public-console.cert.pem
    -   public-console.private\_key.pem
2.  Add the files from step 1 to `/opt/puppetlabs/server/data/console-services/certs/`.

3.  Use the console to edit the parameters of the `puppet_enterprise::profile::console` class.

    1.  Click **Classification**, and in the **PE Infrastructure** group, select the **PE Console** group.

    2.  On the **Configuration** tab, in the `puppet_enterprise::profile::console` class, add the following parameters:

        |Parameter|Value|
        |---------|-----|
        |`browser_ssl_cert`|`/opt/puppetlabs/server/data/console-services/certs/public-console.cert.pem`|
        |`browser_ssl_private_key`|`/opt/puppetlabs/server/data/console-services/certs/public-console.private_key.pem`|

    3.  Commit changes.

4.  Run Puppet.

5.  When the run is complete, restart the console and the nginx service for the changes to take effect.

    ```
    puppet resource service pe-console-services ensure=stopped
    puppet resource service pe-console-services ensure=running
    puppet resource service pe-nginx ensure=stopped
    puppet resource service pe-nginx ensure=running
    ```


You should now be able to navigate to your console and see the custom certificate in your browser.

**Parent topic:**[SSL and certificates](ssl_and_certificates.md)

