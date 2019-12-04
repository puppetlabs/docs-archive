---
layout: default
title: "Regenerate certs for PuppetDB (split installs)"
canonical: "/pe/latest/regenerate_certs_puppetdb.html"
---


If you encounter a security vulnerability, or need to change your certs for some other reason (for example, you have a hostname change), you can regenerate the certificates and security credentials (private and public keys) generated for PuppetDB.

You can regenerate all certificates for the **PuppetDB only**, including the certificates and keys for associated services running on PuppetDB. 

>**Note:** If you've experienced an unforeseen security vulnerability and need to regenerate all the certificates and security credentials in your infrastructure, refer to [Regenerating certs and security credentials in split Puppet Enterprise deployments](./trouble_regenerate_certs_split.html) for complete instructions. 
>
>On monolithic installs, the Puppet master shares an agent cert and security credentials with the PuppetDB and the PE console. For a [monolithic install](./trouble_regenerate_certs_split.html), you must regenerate all certs and security credentials.

**Before you begin,** review the following information:

- You must be logged in as a root, (or in the case of Windows agents, as an account with Administrator Privileges) to make these changes.

- If you encounter any errors during steps that involve `service stop/start`, `rm`, `cp`, or `chmod` commands, you should diagnose these before continuing, as the success each step is very important to the success of the next step.

- In the following instructions, when `<CERTNAME>` is used, it refers to the Puppet master's certname. To find this value, run `puppet config print certname` before starting.

- Unless otherwise indicated, all commands are run on the PuppetDB server.

1. On the PuppetDB node, back up the following directories:

   * `/etc/puppetlabs/puppet/ssl/`

   * `/etc/puppetlabs/puppetdb/ssl/`

   * `/opt/puppetlabs/server/data/postgresql/9.4/data/certs/*`

2. On the PuppetDB node, shut down all PE-related services with the following commands:

        puppet resource service puppet ensure=stopped
        puppet resource service pe-puppetdb ensure=stopped
        puppet resource service pe-postgresql ensure=stopped
        puppet resource service mcollective ensure=stopped

3. On the PuppetDB node, delete the Puppet agent's SSL cert and security credentials.

        rm -rf /etc/puppetlabs/puppet/ssl/*

4. On the Puppet master, or CA server, remove the cert for the PuppetDB node.

        puppet cert clean <PUPPETDB CERTNAME>

5. On the Puppet master, remove the cached catalog.

        rm -f /opt/puppetlabs/puppet/cache/client_data/catalog/<CERTNAME>.json

6. On the PuppetDB node, generate security credentials and request a new certificate from the CA Puppet master. These certs will end up in `/etc/puppetlabs/puppet/ssl`.

        puppet agent --test --no-daemonize --noop

   > **Note**: This agent run will not complete successfully, but it is necessary to set up the agent certificate for the PuppetDB node. You will see some errors about node definition and the inability to submit facts due to PuppetDB being offline. You can ignore these.

7. On the PuppetDB node, delete puppetDB’s SSL cert and security credentials.

        rm -rf /etc/puppetlabs/puppetdb/ssl/*

8. On the PuppetDB node, copy the Puppet agent's certs and security credentials to the PuppetDB SSL directory. 

        cp /etc/puppetlabs/puppet/ssl/certs/<CERTNAME>.pem /etc/puppetlabs/puppetdb/ssl/<CERTNAME>.cert.pem
        cp /etc/puppetlabs/puppet/ssl/public_keys/<CERTNAME>.pem /etc/puppetlabs/puppetdb/ssl/<CERTNAME>.public_key.pem
        cp /etc/puppetlabs/puppet/ssl/private_keys/<CERTNAME>.pem /etc/puppetlabs/puppetdb/ssl/<CERTNAME>.private_key.pem
        
10. On the PuppetDB node, create PuppetDB's .pk8 cert.

         cd /etc/puppetlabs/puppetdb/ssl
         openssl pkcs8 -topk8 -inform PEM -outform DER -in /etc/puppetlabs/puppetdb/ssl<CERTNAME>.private_key.pem -out /etc/puppetlabs/puppetdb/ssl<CERTNAME>.private_key.pk8 -nocrypt
        chown -R pe-puppetdb:pe-puppetdb /etc/puppetlabs/puppetdb/ssl

9. On the PuppetDB node, clear the certs and security credentials from the PostgreSQL certs directory.

         rm -rf /opt/puppetlabs/server/data/postgresql/9.4/data/certs/*

10. On the PuppetDB node, copy the certs and security credentials to the PostgreSQL certs directory.

        cp /etc/puppetlabs/puppet/ssl/certs/<CERTNAME>.pem /opt/puppetlabs/server/data/postgresql/9.4/data/certs/_local.cert.pem
        cp /etc/puppetlabs/puppet/ssl/private_keys/<CERTNAME>.pem /opt/puppetlabs/server/data/postgresql/9.4/data/certs/_local.private_key.pem
        chmod 400 /opt/puppetlabs/server/data/postgresql/9.4/data/certs/*
        chown pe-postgres:pe-postgres /opt/puppetlabs/server/data/postgresql/9.4/data/certs/*

11. On the PuppetDB node, restart all PE-related services with the following commands:

        puppet resource service puppet ensure=running
        puppet resource service pe-puppetdb ensure=running
        puppet resource service pe-postgresql ensure=running
        puppet resource service mcollective ensure=running