# Regenerate the console certificate

The console certificate expires every 824 days. Regenerate the console certificate when it's nearing or past expiration, or if the certificate is corrupted and you're unable to access the console.

After you delete the existing console certificate and its artifacts, a new certificate is generated automatically on the next Puppet run.

1.  On your master, run:

    ```
    rm /etc/puppetlabs/puppet/ssl/ca/signed/console-cert.pem /etc/puppetlabs/puppet/ssl/certs/console-cert.pem /etc/puppetlabs/puppet/ssl/private_keys/console-cert.pem /etc/puppetlabs/puppet/ssl/public_keys/console-cert.pem
    ```

2.  Run Puppet, `puppet agent -t`, or wait for the next Puppet run.


