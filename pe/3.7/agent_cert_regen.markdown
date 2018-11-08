---
layout: default
title: "PE 3.7 » Security and SSL"
subtitle: "Regenerating a Puppet Agent Certificate"
canonical: "/pe/latest/agent_cert_regen.html"
---

From time to time, you may encounter a situation in which you need to regenerate a certificate for a Puppet agent node. Perhaps there is a security vulnerability in your infrastructure that you can remediate with a certificate regeneration, or maybe you're receiving strange SSL errors on your Puppet agent node that are preventing you from performing normal operations. 

The following steps explain how to regenerate a certificate for a Puppet agent node using PE's built-in certificate authority (CA).

**Unless otherwise indicated, the following steps should be performed on your agent nodes**. 

1. Back up the `/etc/puppetlabs/puppet/ssl/` directory.

   If something goes wrong, you may need to restore these directories so your deployment can stay functional. **However,** if you needed to regenerate your certs for security reasons and couldn't, you should contact Puppet Labs support as soon as you restore service so we can help you secure your site.

2. Stop the Puppet agent and MCollective services.

   ~~~
   puppet resource service pe-puppet ensure=stopped
   puppet resource service pe-mcollective ensure=stopped
   ~~~
   
3. Delete the agent's SSL directory. On \*nix nodes, run `rm -rf /etc/puppetlabs/puppet/ssl`.
 
   On Windows nodes, delete the `$confdir\ssl` directory, using the [Administrator confdir](https://docs.puppetlabs.com/pe/3.7/agent_cert_regen.html).
    
4. Remove the cached catalog. on \*nix nodes, run `rm -f /var/opt/lib/pe-puppet/client_data/catalog/<CERT NAME>.json`. 

   On Windows nodes, delete the `$client_datadir\catalog\<CERTNAME>.json` file, using the [Administrator confdir](https://docs.puppetlabs.com/pe/3.7/agent_cert_regen.html).

5. Re-start the Puppet agent and MCollective services.
   
   ~~~
   puppet resource service pe-puppet ensure=running
   puppet resource service pe-mcollective ensure=running
   ~~~

   Once Puppet agent starts, it will automatically generate keys and request a new certificate from the CA Puppet master.
   
6. If you are not using autosigning, you will need to sign each agent node's certificate request. You can do this [with the PE console's request manager](./console_cert_mgmt.html), or by logging into the CA Puppet master server, running `puppet cert list` to see pending requests, and running `puppet cert sign <NAME>` to sign requests.

Once the Puppet agent node’s certificate is signed, you can either manually kick off a Puppet run from the console or command line, or wait for the agent to run based on the [runinterval](/references/3.7.latest/configuration.html#runinterval), the default of which is 30 minutes. At this point, the agent will perform a full catalog run and can resume its role in your PE deployment. 
