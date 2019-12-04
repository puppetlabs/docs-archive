# Regenerate Puppet agent certificates

From time to time, you might encounter a situation in which you need to regenerate a certificate for an agent. Perhaps there is a security vulnerability in your infrastructure that you can remediate with a certificate regeneration, or maybe you're receiving SSL errors on your agent that are preventing you from performing normal operations.

## About this task

Unless otherwise indicated, the following steps should be performed on your agent node.

## Procedure

1.  On the Puppet master, clear the cert for the agent node. Run `puppet cert clean <CERTNAME>`.

2.  Back up the `/etc/puppetlabs/puppet/ssl/` directory.

    If something goes wrong, you may need to restore these directories so your deployment can stay functional. However, if you needed to regenerate your certs for security reasons and couldn't, you should contact Puppet support as soon as you restore service so we can help you secure your site.

3.  Stop the Puppet agent, MCollective, and PXP agent services.

    ```
    
       puppet resource service puppet ensure=stopped
       puppet resource service mcollective ensure=stopped
       puppet resource service pxp-agent ensure=stopped
    			   
    ```

4.  Delete the agent's SSL directory.

    -   On \*nix nodes, run `rm -rf /etc/puppetlabs/puppet/ssl`
    -   On Windows nodes, delete the `$confdir\ssl` directory, using the Administrator confdir.
5.  Remove the agent's cached catalog.

    -   On \*nix nodes, run `rm -f /opt/puppetlabs/puppet/cache/client_data/catalog/<CERTNAME>.json`
    -   On Windows nodes, delete the `$client_datadir\catalog\<CERTNAME>.json` file, using the Administrator confdir.
6.  Re-start the Puppet agent and MCollective services.

    ```
    puppet resource service puppet ensure=running
    puppet resource service mcollective ensure=running
    ```

    After the agent starts, it will automatically generate keys and request a new certificate from the CA Puppet master.

7.  If you are not using autosigning, you will need to sign each agent node's certificate request. You can do this with the console's request manager, or by logging into the CA Puppet master server, running

    ```
    puppet cert list 
    puppet cert sign <NAME>
    ```

    After the agent node’s certificate is signed, you can either manually kick off a Puppet run from the console or command line, or wait for the agent to run based on the runinterval, the default of which is 30 minutes. At this point, the agent will perform a full catalog run, restart the PXP agent service, and will resume its role in your PE deployment.


