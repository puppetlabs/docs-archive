---
layout: default
title: "Regenerate certs for the PE console (split install)"
canonical: "/pe/latest/regenerate_certs_console.html"
---

If you encounter a security vulnerability, or need to change your certs for some other reason (for example, you have a hostname change), you can regenerate the certificates and security credentials (private and public keys) generated for the PE console.

You can regenerate all certificates for the **PE console only**, including the certificates and keys for associated services running on PuppetDB. 

>**Note:** If you've experienced an unforeseen security vulnerability and need to regenerate all the certificates and security credentials in your infrastructure, refer to [Regenerating certs and security credentials in split Puppet Enterprise deployments](./trouble_regenerate_certs_split.html) for complete instructions. 
>
>On monolithic installs, the Puppet master shares an agent cert and security credentials with the PuppetDB and the PE console. For a [monolithic install](./trouble_regenerate_certs_split.html), you must regenerate all certs and security credentials.

**Before you begin,** review the following information:

- You must be logged in as a root, (or in the case of Windows agents, as an account with Administrator Privileges) to make these changes.

- If you encounter any errors during steps that involve `service stop/start`, `rm`, `cp`, or `chmod` commands, you should diagnose these before continuing, as the success each step is very important to the success of the next step.

- In the following instructions, when `<CERTNAME>` is used, it refers to the Puppet master's certname. To find this value, run `puppet config print certname` before starting.

- Unless otherwise indicated, all commands are run on the PE console server.

1. On the console node, back up the following directories:

   * `/etc/puppetlabs/puppet/ssl/`

   * `/opt/puppetlabs/server/data/console-services/certs`

2. On the console node, shut down all PE-related services with the following commands:

        puppet resource service puppet ensure=stopped
        puppet resource service pe-console-services ensure=stopped
        puppet resource service pe-nginx ensure=stopped
        puppet resource service mcollective ensure=stopped

3. On the console node, delete the Puppet agent's SSL cert and security credentials.

        rm -rf /etc/puppetlabs/puppet/ssl/*

4. On the Puppet master, or CA server, remove the cert for the PE console node.

        puppet cert clean <CERTNAME>

5. On the Puppet master, remove the cached catalog.

        rm -f /opt/puppetlabs/puppet/cache/client_data/catalog/<CERTNAME>.json

6. On the console node, generate security credentials and request a new certificate from the CA Puppet master. These certs will end up in `/etc/puppetlabs/puppet/ssl`.

        puppet agent --test --no-daemonize --noop

   > **Note**: This agent run will not complete successfully, but it is necessary to set up the agent certificate for the node. You will see some errors about node definition and the inability to execute http requests due to the console being offline. You can ignore these.

7. On the console node, purge the console-services directory.

        rm -rf /opt/puppetlabs/server/data/console-services/certs/*

8. On the PE console node, copy the Puppet agent's cert and security credentials to the console-services cert directory.
        
        cp /etc/puppetlabs/puppet/ssl/certs/<CERTNAME>.pem /opt/puppetlabs/server/data/console-services/certs/<CERTNAME>.cert.pem
        cp /etc/puppetlabs/puppet/ssl/public_keys/<CERTNAME>.pem /opt/puppetlabs/server/data/console-services/certs/<CERTNAME>.public_key.pem
        cp /etc/puppetlabs/puppet/ssl/private_keys/<CERTNAME>.pem /opt/puppetlabs/server/data/console-services/certs/<CERTNAME>.private_key.pem
        
        
9. Create the console-services .pk8 cert.

        cd /opt/puppetlabs/server/data/console-services/certs/
        openssl pkcs8 -topk8 -inform PEM -outform DER -in /opt/puppetlabs/server/data/console-services/certs/<CERTNAME>.private_key.pem -out /opt/puppetlabs/server/data/console-services/certs/<CERTNAME>.private_key.pk8 -nocrypt
        chown -R pe-console-services:pe-console-services /opt/puppetlabs/server/data/console-services/certs/

10. On the console node, ensure the PE console can access the new credentials.

        chown -R pe-console-services:pe-console-services /opt/puppetlabs/server/data/console-services/certs


11. On the PE console node, restart all PE-related services with the following commands:

        puppet resource service puppet ensure=running
        puppet resource service pe-nginx ensure=running
        puppet resource service pe-console-services ensure=running
        puppet resource service mcollective ensure=running


******

**Related links:**

- [Configuring the PE console to use a custom Diffie-Hellman file](./trouble_dh_generate.html).