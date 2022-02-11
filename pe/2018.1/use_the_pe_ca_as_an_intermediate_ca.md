---
author: Melissa Amos <melissa.amos@puppet.com\>
---

# Use the PE CA as an intermediate CA

PE can operate as an intermediate CA to an external root CA. \(It can't be an intermediate to an intermediate.\) In this configuration, the PE CA is left enabled and it can automatically accept CSRs, distribute certificates to agents, and use the standard `puppet cert` command to sign certificates.

## Before you begin

1.  You must have an intermediate CA certificate from your external root CA.

2.  Back up your infrastructure.

3.  Stop all Puppet services.

    ```
    puppet resource service puppet ensure=stopped
    puppet resource service pe-puppetserver ensure=stopped
    puppet resource service pe-activemq ensure=stopped
    puppet resource service mcollective ensure=stopped
    puppet resource service pe-puppetdb ensure=stopped
    puppet resource service pe-postgresql ensure=stopped
    puppet resource service pe-console-services ensure=stopped
    puppet resource service pe-nginx ensure=stopped
    puppet resource service pe-orchestration-services ensure=stopped
    puppet resource service pxp-agent ensure=stopped
    
    ```

4.  On the master, remove existing `.pem` files: `find /etc/puppetlabs/puppet/ssl -name '*.pem' -delete`


## About this task

There are some limitations to this configuration:

-   Agent-side CRL checking is not possible, although Puppet Server still verifies the CRL.

-   The CA certificate bundle \(the external root CA combined with the intermediate CA certificate\) must be distributed to the agents manually, ideally before Puppet runs.


CAUTION:

This procedure destroys all certificates currently in use in your infrastructure and prevents normal operation until certificates are restored. Time performing this task accordingly.

## Configure PE to use the new certificates

Place and configure certificates on your Puppet Server.

### About this task

Perform these steps on your Puppet Server.

### Procedure

1.  Put these files in the locations indicated: 

    |File|Location|Notes|
    |----|--------|-----|
    |intermediate CA certificate|/etc/puppetlabs/puppet/ssl/ca/ca\_crt.pem| |
    |intermediate CA key|/etc/puppetlabs/puppet/ssl/ca/ca\_key.pem|Can't have a passphrase, because the PE CA can't provide one when using the key.|
    |root CA certificate|/etc/puppetlabs/puppet/ssl/ca/root\_crt.pem|Can be placed anywhere, because Puppet Server doesn't use it directly, but the rest of these instructions assume you placed it as shown.|

    **Note:** All certificate files must have their ownership set to `pe-puppet:pe-puppet` and have permissions of `0600`.

2.  Generate the CA bundle, by combining the root CA and intermediate CA certificates into one PEM file.

    ```
    cd /etc/puppetlabs/puppet/ssl/ca
    cat ca_crt.pem root_crt.pem > ../certs/ca.pem
    ```

3.  Update the CA serial file: `printf '%04x' 3 > /etc/puppetlabs/puppet/ssl/ca/serial`

4.  Generate a certificate for Puppet Server to use, including any `dns_alt_names` that the server must service.

    ```
    puppet cert generate puppetserver.my.domain.net --dns_alt_names=puppetserver,puppet
    ```

5.  Configure a CRL file from the root CA.

    If you have a pre-generated CRL from the root CA, install it into `/etc/puppetlabs/puppet/ssl/ca/ca_crl.pem`.

    If you don't have a pre-generated CRL from the root CA, create one by running `puppet cert generate fakehost` and then revoking this certificate with `puppet cert clean fakehost`.

6.  Re-generate the certificate inventory file.

    ```
    puppet cert reinventory
    ```


## Reconfigure PE infrastructure

After replacing the public key infrastructure, you must regenerate all required certificates.

### About this task

Perform these steps on your master.

### Procedure

1.  Set `certificate revocation` to `false` in the main section of `puppet.conf`.

    ```
    [main]
    certificate_revocation = false
    ```

2.  Remove the cached cataog: `rm -f /opt/puppetlabs/puppet/cache/client_data/catalog/<PUPPET MASTER FQDN>.json`

3.  Reconfigure PE: `puppet infrastructure configure`

4.  Run Puppet: `puppet agent -t `


## Update agents to use the new CA

Prepare agents to use the new CA configuration by copying the CA bundle in place and configuring certificate revocation.

### Before you begin

Back up the `/etc/puppetlabs/puppet/ssl/` directory. If something goes wrong, you might need to restore these directories so your deployment remains functional.

**Tip:** If you were regenerating certs for security reasons and the process failed, contact Support as soon as you restore service so we can help you secure your site.

### About this task

Complete these steps on each agent, unless otherwise indicated.

### Procedure

1.  Stop Puppet services:

    ```
    puppet resource service puppet ensure=stopped
    puppet resource service mcollective ensure=stopped
    puppet resource service pxp-agent ensure=stopped
    # Stop pe-puppetserver if the agent is a compile master
    puppet resource service pe-puppetserver ensure=stopped
    ```

2.  Copy the CA bundle you created to `/etc/puppetlabs/puppet/ssl/certs/ca.pem`.

    **Note:** If you copy this file into place before the first Puppet run, you won't recieve any errors. If you attempt a Puppet run prior to this file being present, you receive errors because the auto-distributed ca.pem file doesn't include the root CA.

3.  On your master, set `certificate revocation` to `false` in the main section of `puppet.conf`:

    ```
    [main]
    certificate_revocation = false
    ```

4.  Remove the certificate file, certificate revocation list \(CRL\), and the agent's cached catalog:

    ```
    find /etc/puppetlabs/puppet/ssl -name "$(puppet config print certname).pem" -or -name crl.pem -delete
    rm -f "/opt/puppetlabs/puppet/cache/client_data/catalog/$(puppet config print certname).json"
    ```

5.  Restart the agent service: `puppet resource service puppet ensure=running`

6.  If you aren't using autosigning, from the console or your master's command line, sign the agent's certificate signing request.

7.  From the console or your master's command line, run Puppet: `puppet agent -t`


### Results

The agent performs a full catalog run, restarts the PXP agent service, and resumes its role in your deployment.

