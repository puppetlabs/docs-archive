---
layout: default
title: "Regenerating a Puppet agent certificate"
canonical: "/pe/latest/agent_cert_regen.html"
---

From time to time, you may encounter a situation in which you need to regenerate a certificate for a Puppet agent node. Perhaps there is a security vulnerability in your infrastructure that you can remediate with a certificate regeneration, or maybe you're receiving strange SSL errors on your Puppet agent node that are preventing you from performing normal operations.

The following steps explain how to regenerate a certificate for a Puppet agent node using PE's built-in certificate authority (CA).

**Unless otherwise indicated, the following steps should be performed on your agent nodes**.

1. **On the Puppet master**, clear the cert for the agent node. Run `puppet cert clean <CERTNAME>`.

2. Back up the `/etc/puppetlabs/puppet/ssl/` directory.

   If something goes wrong, you may need to restore these directories so your deployment can stay functional. **However,** if you needed to regenerate your certs for security reasons and couldn't, you should contact Puppet support as soon as you restore service so we can help you secure your site.

3. Stop the Puppet agent, MCollective, and pxp-agent services.

   ~~~
   puppet resource service puppet ensure=stopped
   puppet resource service mcollective ensure=stopped
   puppet resource service pxp-agent ensure=stopped
   ~~~

4. Delete the agent's SSL directory. On \*nix nodes, run `rm -rf /etc/puppetlabs/puppet/ssl`.

   On Windows nodes, delete the `$confdir\ssl` directory, using the [Administrator confdir]({{puppet}}/dirs_confdir.html#location).

5. Remove the agent's cached catalog. On \*nix nodes, run `rm -f /opt/puppetlabs/puppet/cache/client_data/catalog/<CERTNAME>.json`.

   On Windows nodes, delete the `$client_datadir\catalog\<CERTNAME>.json` file, using the [Administrator confdir]({{puppet}}/dirs_confdir.html#location).

6. Re-start the Puppet agent and MCollective services.

   ~~~
   puppet resource service puppet ensure=running
   puppet resource service mcollective ensure=running
   ~~~

   After the Puppet agent starts, it will automatically generate keys and request a new certificate from the CA Puppet master.

7. If you are not using autosigning, you will need to sign each agent node's certificate request. You can do this [with the PE console's request manager](./console_cert_mgmt.html), or by logging into the CA Puppet master server, running `puppet cert list` to see pending requests, and running `puppet cert sign <NAME>` to sign requests.


After the Puppet agent node’s certificate is signed, you can either manually kick off a Puppet run from the console or command line, or wait for the agent to run based on the [runinterval]({{puppet}}/configuration.html#runinterval), the default of which is 30 minutes. At this point, the agent will perform a full catalog run, restart the PXP agent service, and will resume its role in your PE deployment.
