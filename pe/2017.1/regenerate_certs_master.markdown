---
layout: default
title: "Regenerate certs for the Puppet master (split installs)"
canonical: "/pe/latest/regenerate_certs_master.html"
---

If you encounter a security vulnerability, or need to change your certs for some other reason (for example, you have a hostname change), you can regenerate the certificates and security credentials (private and public keys) generated for the Puppet master.

You can regenerate all certificates for the **Puppet master server only**, including the certificates and keys for associated services running on the Puppet master. 

>**Note:** If you've experienced an unforeseen security vulnerability and need to regenerate all the certificates and security credentials in your infrastructure, refer to [Regenerating certs and security credentials in split Puppet Enterprise deployments](./trouble_regenerate_certs_split.html) for complete instructions. 
>
>On monolithic installs, the Puppet master shares an agent cert and security credentials with the PuppetDB and the PE console. For a [monolithic install](./trouble_regenerate_certs_split.html), you must regenerate all certs and security credentials.
**Before you begin,** review the following information:

- This his document should not be used to regenerate certificates for compile masters. Instead, refer to the [compile master cert regen instructions](./compile_master_cert_regen.html).

- You must be logged in as a root, (or in the case of Windows agents, as an account with Administrator Privileges) to make these changes.

- If you encounter any errors during steps that involve `service stop/start`, `rm`, `cp`, or `chmod` commands, you should diagnose these before continuing, as the success each step is very important to the success of the next step.

- In the following instructions, when `<CERTNAME>` is used, it refers to the Puppet master's certname. To find this value, run `puppet config print certname` before starting.

- Unless otherwise indicated, all commands are run on the Puppet master server.


1. Back up the `/etc/puppetlabs/puppet/ssl/` directory.

   If something goes wrong, you may need to restore these directories so your deployment can stay functional. **However,** if you needed to regenerate your certs for security reasons and couldn't, you should contact Puppet support as soon as you restore service so we can help you secure your site.

2. Shut down all PE-related services with the following commands:

        puppet resource service puppet ensure=stopped
        puppet resource service pe-puppetserver ensure=stopped
        puppet resource service pe-activemq ensure=stopped
        puppet resource service mcollective ensure=stopped
        puppet resource service pe-orchestration-services ensure=stopped
        puppet resource service pxp-agent ensure=stopped

3. Clear the cert and security credentials for the Puppet master.

        puppet cert clean <CERTNAME>

4. Remove the cached catalog.

        rm -f /opt/puppetlabs/puppet/cache/client_data/catalog/<CERTNAME>.json

5. Generate the Puppet master's new certs.

        puppet cert generate <CERTNAME> --dns_alt_names=<DNS_ALT_NAMES>

   >**Note**: Be sure to specify any dns alt names you have. You can find the list of your current dns alt names with `puppet cert list <CERTNAME>`. By default, PE uses `puppet` and `puppet.domain`.

6. Copy the new cert and security credentials to the orchestration-services cert directory.  

        cp /etc/puppetlabs/puppet/ssl/certs/<CERTNAME>.pem /etc/puppetlabs/orchestration-services/ssl/<CERTNAME>.cert.pem
        cp /etc/puppetlabs/puppet/ssl/public_keys/<CERTNAME>.pem /etc/puppetlabs/orchestration-services/ssl/<CERTNAME>.public_key.pem
        cp /etc/puppetlabs/puppet/ssl/private_keys/<CERTNAME>.pem /etc/puppetlabs/orchestration-services/ssl/<CERTNAME>.private_key.pem
        openssl pkcs8 -topk8 -inform PEM -outform DER -in /etc/puppetlabs/orchestration-services/ssl/<CERTNAME>.private_key.pem -out /etc/puppetlabs/orchestration-services/ssl/<CERTNAME>.private_key.pk8 -nocrypt
        chown -R pe-orchestration-services:pe-orchestration-services /etc/puppetlabs/orchestration-services/ssl/

7. Create the orchestration-services .pk8 cert.

        cd /etc/puppetlabs/orchestration-services/ssl
        openssl pkcs8 -topk8 -inform PEM -outform DER -in /etc/puppetlabs/orchestration-services/ssl/<CERTNAME>.private_key.pem -out /etc/puppetlabs/orchestration-services/ssl/<CERTNAME>.private_key.pk8 -nocrypt
        chown -R pe-orchestration-services:pe-orchestration-services /etc/puppetlabs/orchestration-services/ssl/


8. Restart all PE-related services with the following commands:

        puppet resource service puppet ensure=running
        puppet resource service pe-puppetserver ensure=running
        puppet resource service pe-activemq ensure=running
        puppet resource service mcollective ensure=running
        puppet resource service pe-orchestration-services ensure=running
        puppet resource service pxp-agent ensure=running
