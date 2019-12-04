---
author: Isaac Eldridge <isaac.eldridge@puppet.com\>
---

# Individual PE component cert regeneration \(split installs only\)

If you encounter a security vulnerability, or need to change your certificates for some other reason \(for example, if you have a hostname change\), you can regenerate the certificate and security credentials \(private and public keys\) generated for the PE components.

CAUTION:

If you've experienced an unforeseen security vulnerability and need to regenerate all the certificates and security credentials in your infrastructure, refer to Regenerating certs and security credentials in split Puppet Enterprise installations for complete instructions.

On monolithic installs, the Puppet master shares an agent cert and security credentials with the PuppetDB and the console. For a monolithic install, you must regenerate all certs and security credentials, as documented in Regenerating certificates: monolithic installs.

You can regenerate certificates for the following components:

-   The Puppet master

-   PuppetDB

-   The PE console


**Related information**  


[Regenerate certificates in PE: monolithic installs](regenerating_certificates_monolithic_installs.md#)

[Regenerate certificates in PE: split installs](regenerating_certificates_split_installs.md#)

## Regenerate Puppet master certs \(split installs\)

You can regenerate all certificates for the Puppet master server only, including the certificates and keys for associated services running on the Puppet master.

### Before you begin

-   You must be logged in as a root, \(or in the case of Windows agents, as an account with Administrator Privileges\) to make these changes.

-   In the following instructions, when `<CERTNAME>` is used, it refers to the Puppet master's certname. To find this value, run `puppet config print certname` before starting.


### About this task

CAUTION:

If you've experienced an unforeseen security vulnerability and need to regenerate all the certificates and security credentials in your infrastructure, refer to Regenerating certs and security credentials in split Puppet Enterprise installations for complete instructions.

On monolithic installs, the Puppet master shares an agent cert and security credentials with the PuppetDB and the console. For a monolithic install, you must regenerate all certs and security credentials, as documented in Regenerating certificates: monolithic installs.

This document should not be used to regenerate certificates for compile masters. Instead, refer to the compile master cert regen instructions.

If you encounter any errors during steps that involve `service stop/start`, `rm`, `cp`, or `chmod` commands, diagnose these before continuing, as the success of each step is important to the success of the next step.

Unless otherwise indicated, all commands are run on the Puppet master server.

### Procedure

1.  Back up the `/etc/puppetlabs/puppet/ssl/` directory.

    If something goes wrong, you may need to restore these directories so your deployment can stay functional. However, if you needed to regenerate your certs for security reasons and couldn't, you should contact Puppet support as soon as you restore service so we can help you secure your site.

2.  Shut down all PE-related services with the following commands:

    ```
    puppet resource service puppet ensure=stopped
    puppet resource service pe-puppetserver ensure=stopped
    puppet resource service pe-activemq ensure=stopped
    puppet resource service mcollective ensure=stopped
    puppet resource service pe-orchestration-services ensure=stopped
    puppet resource service pxp-agent ensure=stopped
    ```

3.  Clear the cert and security credentials for the Puppet master: `puppet cert clean <CERTNAME>`

4.  Remove the cached catalog: `rm -f /opt/puppetlabs/puppet/cache/client_data/catalog/<CERTNAME>.json`

5.  Generate the Puppet master's new certs: `puppet cert generate <CERTNAME> --dns_alt_names=<DNS_ALT_NAMES>`

    **Note:** Be sure to specify any dns alt names you have. You can find the list of your current DNS alt names with `puppet cert list <CERTNAME>`. By default, PE uses `puppet` and `puppet.domain`.

6.  Copy the new cert and security credentials to the orchestration-services cert directory.

    ```
    cp /etc/puppetlabs/puppet/ssl/certs/<CERTNAME>.pem /etc/puppetlabs/orchestration-services/ssl/<CERTNAME>.cert.pem
    cp /etc/puppetlabs/puppet/ssl/public_keys/<CERTNAME>.pem /etc/puppetlabs/orchestration-services/ssl/<CERTNAME>.public_key.pem
    cp /etc/puppetlabs/puppet/ssl/private_keys/<CERTNAME>.pem /etc/puppetlabs/orchestration-services/ssl/<CERTNAME>.private_key.pem
    openssl pkcs8 -topk8 -inform PEM -outform DER -in /etc/puppetlabs/orchestration-services/ssl/<CERTNAME>.private_key.pem -out /etc/puppetlabs/orchestration-services/ssl/<CERTNAME>.private_key.pk8 -nocrypt
    chown -R pe-orchestration-services:pe-orchestration-services /etc/puppetlabs/orchestration-services/ssl/
    ```

7.  Create the orchestration-services .pk8 cert.

    ```
    cd /etc/puppetlabs/orchestration-services/ssl
    openssl pkcs8 -topk8 -inform PEM -outform DER -in /etc/puppetlabs/orchestration-services/ssl/<CERTNAME>.private_key.pem -out /etc/puppetlabs/orchestration-services/ssl/<CERTNAME>.private_key.pk8 -nocrypt
    chown -R pe-orchestration-services:pe-orchestration-services /etc/puppetlabs/orchestration-services/ssl/
    ```

8.  Restart all PE-related services with the following commands:

    ```
    puppet resource service puppet ensure=running
    puppet resource service pe-puppetserver ensure=running
    puppet resource service pe-activemq ensure=running
    puppet resource service mcollective ensure=running
    puppet resource service pe-orchestration-services ensure=running
    puppet resource service pxp-agent ensure=running
    ```


## Regenerate PuppetDB certs \(split installs\)

You can regenerate all certificates for the PuppetDB only, including the certificates and keys for associated services running on PuppetDB.

### Before you begin

-   You must be logged in as a root, \(or in the case of Windows agents, as an account with Administrator Privileges\) to make these changes.

-   In the following instructions, when `<CERTNAME>` is used, it refers to the Puppet master's certname. To find this value, run `puppet config print certname` before starting.


### About this task

CAUTION:

If you've experienced an unforeseen security vulnerability and need to regenerate all the certificates and security credentials in your infrastructure, refer to Regenerating certs and security credentials in split Puppet Enterprise installations for complete instructions.

On monolithic installs, the Puppet master shares an agent cert and security credentials with the PuppetDB and the console. For a monolithic install, you must regenerate all certs and security credentials, as documented in Regenerating certificates: monolithic installs.

If you encounter any errors during steps that involve `service stop/start`, `rm`, `cp`, or `chmod` commands, diagnose these before continuing, as the success of each step is important to the success of the next step.

Unless otherwise indicated, all commands are run on the PuppetDB server.

### Procedure

1.  On the PuppetDB node, back up the following directories:

    -   `/etc/puppetlabs/puppet/ssl/`
    -   `/etc/puppetlabs/puppetdb/ssl/`
    -   `/opt/puppetlabs/server/data/postgresql/9.6/data/certs/`\*
2.  On the PuppetDB node, shut down all PE-related services with the following commands:

    ```
    puppet resource service puppet ensure=stopped
    puppet resource service pe-puppetdb ensure=stopped
    puppet resource service pe-postgresql ensure=stopped
    puppet resource service mcollective ensure=stopped
    ```

3.  On the PuppetDB node, delete the agent's SSL cert and security credentials: `rm -rf /etc/puppetlabs/puppet/ssl/*`

4.  On the master or CA server, remove the cert for the PuppetDB node: `puppet cert clean <PUPPETDB CERTNAME>`

5.  On the master, remove the cached catalog: `rm -f /opt/puppetlabs/puppet/cache/client_data/catalog/<CERTNAME>.json`

6.  On the PuppetDB node, generate security credentials and request a new certificate from the CA master: `puppet agent --test --no-daemonize --noop`

    These certs will end up in `/etc/puppetlabs/puppet/ssl`.

    **Note:** This agent run will not complete successfully, but it is necessary to set up the agent certificate for the PuppetDB node. You will see some errors about node definition and the inability to submit facts due to PuppetDB being offline. You can ignore these.

7.  On the PuppetDB node, delete PuppetDB’s SSL cert and security credentials: `rm -rf /etc/puppetlabs/puppetdb/ssl/*`

8.  On the PuppetDB node, copy the agent's certs and security credentials to the PuppetDB SSL directory.

    ```
    cp /etc/puppetlabs/puppet/ssl/certs/<CERTNAME>.pem /etc/puppetlabs/puppetdb/ssl/<CERTNAME>.cert.pem
    cp /etc/puppetlabs/puppet/ssl/public_keys/<CERTNAME>.pem /etc/puppetlabs/puppetdb/ssl/<CERTNAME>.public_key.pem
    cp /etc/puppetlabs/puppet/ssl/private_keys/<CERTNAME>.pem /etc/puppetlabs/puppetdb/ssl/<CERTNAME>.private_key.pem
    ```

9.  On the PuppetDB node, create PuppetDB's .pk8 cert.

    ```
     cd /etc/puppetlabs/puppetdb/ssl
     openssl pkcs8 -topk8 -inform PEM -outform DER -in /etc/puppetlabs/puppetdb/ssl<CERTNAME>.private_key.pem -out /etc/puppetlabs/puppetdb/ssl<CERTNAME>.private_key.pk8 -nocrypt
    chown -R pe-puppetdb:pe-puppetdb /etc/puppetlabs/puppetdb/ssl
    ```

10. On the PuppetDB node, clear the certs and security credentials from the PostgreSQL certs directory: `rm -rf /opt/puppetlabs/server/data/postgresql/9.6/data/certs/*`

11. On the PuppetDB node, copy the certs and security credentials to the PostgreSQL certs directory.

    ```
    cp /etc/puppetlabs/puppet/ssl/certs/<CERTNAME>.pem /opt/puppetlabs/server/data/postgresql/9.6/data/certs/_local.cert.pem
    cp /etc/puppetlabs/puppet/ssl/private_keys/<CERTNAME>.pem /opt/puppetlabs/server/data/postgresql/9.6/data/certs/_local.private_key.pem
    chmod 400 /opt/puppetlabs/server/data/postgresql/9.6/data/certs/*
    chown pe-postgres:pe-postgres /opt/puppetlabs/server/data/postgresql/9.6/data/certs/*
    ```

12. On the PuppetDB node, restart all PE-related services with the following commands:

    ```
    puppet resource service puppet ensure=running
    puppet resource service pe-puppetdb ensure=running
    puppet resource service pe-postgresql ensure=running
    puppet resource service mcollective ensure=running
    ```


## Regenerate PE console certs

You can regenerate all certificates for the console only, including the certificates and keys for associated services running on the console.

### Before you begin

-   You must be logged in as a root, \(or in the case of Windows agents, as an account with Administrator Privileges\) to make these changes.

-   In the following instructions, when `<CERTNAME>` is used, it refers to the Puppet master's certname. To find this value, run `puppet config print certname` before starting.


### About this task

CAUTION:

If you've experienced an unforeseen security vulnerability and need to regenerate all the certificates and security credentials in your infrastructure, refer to Regenerating certs and security credentials in split Puppet Enterprise installations for complete instructions.

On monolithic installs, the Puppet master shares an agent cert and security credentials with the PuppetDB and the console. For a monolithic install, you must regenerate all certs and security credentials, as documented in Regenerating certificates: monolithic installs.

If you encounter any errors during steps that involve `service stop/start`, `rm`, `cp`, or `chmod` commands, diagnose these before continuing, as the success of each step is important to the success of the next step.

Unless otherwise indicated, all commands are run on the console server.

### Procedure

1.  On the console node, back up the following directories:

    -   `/etc/puppetlabs/puppet/ssl/`
    -   `/opt/puppetlabs/server/data/console-services/certs`
2.  On the console node, shut down all PE-related services with the following commands:

    ```
    puppet resource service puppet ensure=stopped
    puppet resource service pe-console-services ensure=stopped
    puppet resource service pe-nginx ensure=stopped
    puppet resource service mcollective ensure=stopped
    ```

3.  On the console node, delete the Puppet agent's SSL cert and security credentials: `rm -rf /etc/puppetlabs/puppet/ssl/*`

4.  On the Puppet master, or CA server, remove the cert for the console node: `puppet cert clean <CERTNAME>`

5.  On the Puppet master, remove the cached catalog: `rm -f /opt/puppetlabs/puppet/cache/client_data/catalog/<CERTNAME>.json`

6.  On the console node, generate security credentials and request a new certificate from the CA Puppet master: `puppet agent --test --no-daemonize --noop`

    These certs will end up in `/etc/puppetlabs/puppet/ssl`.

    **Note:** This agent run will not complete successfully, but it is necessary to set up the agent certificate for the node. You will see some errors about node definition and the inability to execute http requests due to the console being offline. You can ignore these.

7.  On the console node, purge the console-services directory: `rm -rf /opt/puppetlabs/server/data/console-services/certs/*`

8.  On the console node, copy the agent's cert and security credentials to the console-services cert directory.

    ```
    cp /etc/puppetlabs/puppet/ssl/certs/<CERTNAME>.pem /opt/puppetlabs/server/data/console-services/certs/<CERTNAME>.cert.pem
    cp /etc/puppetlabs/puppet/ssl/public_keys/<CERTNAME>.pem /opt/puppetlabs/server/data/console-services/certs/<CERTNAME>.public_key.pem
    cp /etc/puppetlabs/puppet/ssl/private_keys/<CERTNAME>.pem /opt/puppetlabs/server/data/console-services/certs/<CERTNAME>.private_key.pem
    ```

9.  Create the console-services .pk8 cert.

    ```
    cd /opt/puppetlabs/server/data/console-services/certs/
    openssl pkcs8 -topk8 -inform PEM -outform DER -in /opt/puppetlabs/server/data/console-services/certs/<CERTNAME>.private_key.pem -out /opt/puppetlabs/server/data/console-services/certs/<CERTNAME>.private_key.pk8 -nocrypt
    chown -R pe-console-services:pe-console-services /opt/puppetlabs/server/data/console-services/certs/
    ```

10. On the console node, ensure the console can access the new credentials.

    ```
    chown -R pe-console-services:pe-console-services /opt/puppetlabs/server/data/console-services/certs
    ```

11. On the console node, restart all PE-related services with the following commands:

    ```
    puppet resource service puppet ensure=running
    puppet resource service pe-nginx ensure=running
    puppet resource service pe-console-services ensure=running
    puppet resource service mcollective ensure=running
    ```


