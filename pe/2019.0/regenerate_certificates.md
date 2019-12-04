# Regenerate certificates in monolithic installations

Regenerating certificates and security credentials—both private and public keys—created by the built-in PE certificate authority can help ensure the security of your installation in certain cases.

## About this task

The process for regenerating certificates varies depending on your goal.

|If your goal is to...|Do this...|
|---------------------|----------|
|Upgrade to the intermediate certificate architecture introduced in Puppet 6.0.|Complete these tasks in order:

1.  [Delete and recreate the certificate authority](regenerate_certificates.md#)

2.  [Regenerate compile master certificates](regenerate_certificates_split.md#), if applicable

3.  [Regenerate agent certificates](regenerate_certificates_split.md#)


|
|Fix a compromised or damaged certificate authority.|
|Fix a compromised compile master certificate or troubleshoot SSL errors on compile masters.|[Regenerate compile master certificates](regenerate_certificates_split.md#)|
|Fix a compromised agent certificate or troubleshoot SSL errors on agent nodes.|[Regenerate agent certificates](regenerate_certificates_split.md#)|
|Specify a new DNS alt name or other trusted data.|[Regenerate master certificates](regenerate_certificates.md#)|

## Delete and recreate the certificate authority

Recreate the certificate authority only if you're upgrading to the new certificate architecture introduced in Puppet 6.0, or if your certificate authority was compromised or damaged beyond repair.

### About this task

|![](bolt-logo-dark.png)|As an alternative to performing these steps manually, on your master logged in as root, run `puppet infrastructure run rebuild_certificate_authority caserver=<CA_SERVER_HOSTNAME>`. If your master operates as your CA server, specify `caserver=localhost`. Running the command with `localhost` avoids the requirement to set up SSH between your master and itself. The `puppet infrastructure run` command leverages built-in Bolt plans to automate certain management tasks. To use this command, you must be able to connect using SSH from your master to any nodes that the command modifies. You can establish an SSH connection using key forwarding, a local key file, or by specifying keys in `.ssh/config` on your master. For more information, see [Bolt OpenSSH configuration options](https://puppet.com/docs/bolt/latest/bolt_configuration_options.html#openssh-configuration-options).

|

CAUTION:

Replacing your certificate authority invalidates all existing certificates in your environment. Complete this task only if and when you're prepared to regenerate certificates for both your infrastructure nodes and your entire agent fleet.

Perform these steps on your master logged in as root.

### Procedure

1.  Back up these directories on your master:

    -   `/etc/puppetlabs/puppet/ssl/`
    -   `/etc/puppetlabs/puppetdb/ssl/`
    -   `/opt/puppetlabs/server/data/console-services/certs/`
    -   `/opt/puppetlabs/server/data/postgresql/9.6/data/certs/`
    -   `/etc/puppetlabs/orchestration-services/ssl`
2.  Stop these PE services:

    ```
    puppet resource service puppet ensure=stopped
    puppet resource service pxp-agent ensure=stopped
    puppet resource service pe-puppetserver ensure=stopped
    puppet resource service pe-bolt-server ensure=stopped
    puppet resource service pe-orchestration-services ensure=stopped
    puppet resource service pe-console-services ensure=stopped
    puppet resource service pe-puppetdb ensure=stopped
    puppet resource service pe-postgresql ensure=stopped
    ```

3.  Remove existing certificate authority files and the cached catalog:

    ```
    rm -rf /etc/puppetlabs/puppet/ssl/*
    rm -f /opt/puppetlabs/puppet/cache/client_data/catalog/<CERTNAME>.json
    ```

4.  Rebuild certificates: `puppet infrastructure configure --no-recover`

5.  Run Puppet: `puppet agent -t`


## Regenerate compile master certificates

Regnerate compile master certificates to fix a compromised certificate or troubleshoot SSL errors on compile masters, or if you recreated your certificate authority.

### Before you begin

Configure Puppet Server with `allow-subject-alt-names` in the `certificate-authority` section of `ca.conf`.

### Procedure

1.  If you did not recreate your certificate authority, you must log into your master of masters as root and remove the compile master certificate: `puppetserver ca clean --certname <COMPILE_MASTER_HOSTNAME>`

2.  Log into the compile master as root and back up the `/etc/puppetlabs/puppet/ssl/` directory:

    ```
    cp -r /etc/puppetlabs/puppet/ssl/ /etc/puppetlabs/puppet/ssl_bak/
    ```

3.  Stop the Puppet agent, Puppet Server, and PXP agent services.

    ```
    puppet resource service puppet ensure=stopped
    puppet resource service pe-puppetserver ensure=stopped
    puppet resource service pxp-agent ensure=stopped			 
    ```

4.  Delete the compile master SSL directory: `rm -rf /etc/puppetlabs/puppet/ssl`

5.  Remove the compile master cached catalog: `rm -f /opt/puppetlabs/puppet/cache/client_data/catalog/<CERTNAME>.json`

6.  Adjust DNS alt names and trusted data, as needed.

7.  Re-start the Puppet agent service: `puppet resource service puppet ensure=running`

    After the agent starts, it automatically generates keys and request a new certificate from the Puppet CA.

8.  Log into your master of masters as root and sign the compile master's certificate request: `puppetserver ca sign --certname <COMPILE_MASTER_HOSTNAME>`

9.  Log into your compile master as root and run Puppet: `puppet agent -t`


### Results

PE performs a full catalog run, and the compile master resumes its role in your deployment.

## Regenerate agent certificates

Regenerate agent certificates to fix a compromised certificate or troubleshoot SSL errors on agents, or if you recreated your certificate authority.

### About this task

|![](bolt-logo-dark.png)|As an alternative to performing these steps manually, on your master logged in as root, run `puppet infrastructure run regenerate_agent_certificate agent=<AGENT_HOSTNAME> caserver=<CA_SERVER_HOSTNAME>`. Your CA server is usually your master.

You can specify this optional parameter:

-   `dns_alt_names` – Comma-separated list of alternate DNS names to be added to the certificates generated for your agents.

**Important:** To use the `dns_alt_names` parameter, you must configure Puppet Server with `allow-subject-alt-names` in the `certificate-authority` section of `ca.conf`.


The `puppet infrastructure run` command leverages built-in Bolt plans to automate certain management tasks. To use this command, you must be able to connect using SSH from your master to any nodes that the command modifies. You can establish an SSH connection using key forwarding, a local key file, or by specifying keys in `.ssh/config` on your master. For more information, see [Bolt OpenSSH configuration options](https://puppet.com/docs/bolt/latest/bolt_configuration_options.html#openssh-configuration-options).

|

Unless otherwise indicated, perform these steps on the agent node that you're regenerating certificates for.

### Procedure

1.  If you did not recreate your certificate authority, you must log into your master and clear the cert for the agent node: `puppetserver ca clean --certname <CERTNAME>`

2.  On the agent, back up the `/etc/puppetlabs/puppet/ssl/` directory.

    If something goes wrong, you might need to restore these directories so your deployment remains functional.

3.  Stop the Puppet agent and PXP agent services.

    ```
    puppet resource service puppet ensure=stopped
    puppet resource service pxp-agent ensure=stopped
    ```

4.  Delete the agent SSL directory.

    -   On \*nix nodes: `rm -rf /etc/puppetlabs/puppet/ssl`
    -   On Windows nodes, use the Administrator confdir to delete the `$confdir\ssl` directory.
5.  Remove the agent's cached catalog.

    -   On \*nix nodes: `rm -f /opt/puppetlabs/puppet/cache/client_data/catalog/<CERTNAME>.json`
    -   On Windows nodes, use the Administrator confdir to delete the `$client_datadir\catalog\<CERTNAME>.json` file.
6.  Re-start the Puppet agent service: `puppet resource service puppet ensure=running`

    After the agent starts, it automatically generates keys and request a new certificate from the Puppet CA.

7.  If you aren't using autosigning, sign each agent node's certificate request using the console's request manager, or from your master:

    ```
    puppetserver ca list 
    puppetserver ca sign --certname <NAME>
    ```

    **Note:** For more information about autosigning, see [Autosigning certificate requests](https://puppet.com/docs/puppet/6.0/ssl_autosign.html).

8.  From the console or command line, run Puppet on the node.


### Results

The agent performs a full catalog run, restarts the PXP agent service, and resumes its role in your PE deployment.

## Regenerate master certificates

Regenerate master certificates to specify a new DNS alt name or other trusted data.

### About this task

|![](bolt-logo-dark.png)|As an alternative to performing these steps manually, on your master logged in as root, run `puppet infrastructure run regenerate_master_certificate`.

You can specify this optional parameter:

-   `dns_alt_names` – Comma-separated list of alternate DNS names to be added to the certificates generated for your master.

**Important:** To use the `dns_alt_names` parameter, you must configure Puppet Server with `allow-subject-alt-names` in the `certificate-authority` section of `ca.conf`.


The `puppet infrastructure run` command leverages built-in Bolt plans to automate certain management tasks. To use this command, you must be able to connect using SSH from your master to any nodes that the command modifies. You can establish an SSH connection using key forwarding, a local key file, or by specifying keys in `.ssh/config` on your master. For more information, see [Bolt OpenSSH configuration options](https://puppet.com/docs/bolt/latest/bolt_configuration_options.html#openssh-configuration-options).

|

Perform these steps on your master logged in as root.

### Procedure

1.  Stop the Puppet agent and PXP agent services.

    ```
    puppet resource service puppet ensure=stopped
    puppet resource service pxp-agent ensure=stopped			 
    ```

2.  Back up the `/etc/puppetlabs/puppet/ssl/` directory:

    ```
    cp -r /etc/puppetlabs/puppet/ssl/ /etc/puppetlabs/puppet/ssl_bak/
    ```

3.  Remove the cached catalog: `rm -f /opt/puppetlabs/puppet/cache/client_data/catalog/<CERTNAME>.json`

4.  Revoke the master certificate: `puppetserver ca clean --certname <CERTNAME>`

5.  Delete the master certificates: `find /etc/puppetlabs/puppet/ssl -name <CERTNAME>.pem -delete`

6.  Adjust DNS alt names and trusted data, as needed.

7.  Stop the Puppet Server service: `puppet resource service pe-puppetserver ensure=stopped`

    **Tip:** If you're running PE 2019.0.2 or a newer version, you do not need to stop the Puppet Server service and can skip step 7.

8.  Rebuild certificates: `puppet infrastructure configure --no-recover`

9.  Run Puppet: `puppet agent -t`


