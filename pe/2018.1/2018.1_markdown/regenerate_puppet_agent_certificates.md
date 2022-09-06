# Regenerate Puppet agent certificates

From time to time, you might encounter a situation in which you need to regenerate a certificate for an agent. Perhaps there is a security vulnerability in your infrastructure that you can remediate with a certificate regeneration, or maybe you're receiving SSL errors on your agent that are preventing you from performing normal operations.

## About this task

|![](bolt-logo-dark.png)|As an alternative to performing these steps manually, on your master logged in as root, run `puppet infrastructure run regenerate_agent_certificate agent=<AGENT_HOSTNAME> caserver=<CA_SERVER_HOSTNAME>`. Your CA server is usually your master. \(If you're running PE version 2018.1.11 or newer, omit the `caserver` parameter.\)

 To regenerate certificates for multiple agents, use a comma-separated list of the agents' hostnames. For example, `puppet infrastructure run regenerate_agent_certificate agent=`agent1.example.net,agent2.example.net,agent3.example.net``

 You can specify these optional parameters:

-   `tmpdir` — Path to a directory to use for uploading and executing temporary files.
-   `dns_alt_names` – Comma-separated list of alternate DNS names to be added to the certificates generated for your agents.

**Important:** To use the `dns_alt_names` parameter, you must configure Puppet Server with `allow-subject-alt-names` in the `certificate-authority` section of `pe-puppet-server.conf`. To ensure naming consistency, if your `puppet.conf` file includes a `dns_alt_names` entry, you must include the `dns_alt_names` parameter and pass in all alt names included in the entry when regenerating certificates.


 The `puppet infrastructure run` command leverages built-in Bolt plans to automate certain management tasks. To use this command, you must be able to connect using SSH from your master to any nodes that the command modifies. You can establish an SSH connection using key forwarding, a local key file, or by specifying keys in `.ssh/config` on your master. For more information, see [Bolt OpenSSH configuration options](https://puppet.com/docs/bolt/latest/bolt_configuration_options.html#openssh-configuration-options).

 To view all available parameters, use the `--help` flag. The logs for all `puppet infrastructure run` Bolt plans are located at `/var/log/puppetlabs/installer/bolt_info.log`.

|

Unless otherwise indicated, the following steps should be performed on your agent node.

## Procedure

1.  On the master, clear the cert for the agent node. Run `puppet cert clean <CERTNAME>`.

2.  On the agent, back up the `/etc/puppetlabs/puppet/ssl/` directory.

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

    **Tip:** For information about autosigning, see [Autosigning certificate requests](https://puppet.com/docs/puppet/5.5/ssl_autosign.html).


