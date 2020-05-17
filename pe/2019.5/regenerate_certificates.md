# Regenerate infrastructure certificates

Regenerating certificates and security credentials—both private and public keys—created by the built-in PE certificate authority can help ensure the security of your installation in certain cases.

## About this task

The process for regenerating certificates varies depending on your goal.

|If your goal is to...|Do this...|
|---------------------|----------|
|Upgrade to the intermediate certificate architecture introduced in Puppet 6.0.|Complete these tasks in order:

1.  [Delete and recreate the certificate authority](regenerate_certificates.md#)

2.  [Regenerate compiler certificates](regenerate_certificates.md#), if applicable

3.  [Regenerate \*nix agent certificates](regenerate_certificates.md#) or [Regenerate Windows agent certificates](regenerate_certificates.md#)


|
|Fix a compromised or damaged certificate authority.|
|Fix a compromised compiler certificate or troubleshoot SSL errors on compilers.|[Regenerate compiler certificates](regenerate_certificates.md#)|
|Fix a compromised agent certificate or troubleshoot SSL errors on agent nodes.|[Regenerate \*nix agent certificates](regenerate_certificates.md#) or [Regenerate Windows agent certificates](regenerate_certificates.md#)|
|Specify a new DNS alt name or other trusted data.|[Regenerate master certificates](regenerate_certificates.md#)|

## Delete and recreate the certificate authority

Recreate the certificate authority only if you're upgrading to the new certificate architecture introduced in Puppet 6.0, or if your certificate authority was compromised or damaged beyond repair.

### Before you begin

The `puppet infrastructure run` command leverages built-in Bolt plans to automate certain management tasks. To use this command, you must be able to connect using SSH from your master to any nodes that the command modifies. You can establish an SSH connection using key forwarding, a local key file, or by specifying keys in `.ssh/config` on your master. For more information, see [Bolt OpenSSH configuration options](https://puppet.com/docs/bolt/latest/bolt_configuration_options.html#openssh-configuration-options).

### About this task

To view all available parameters, use the `--help` flag. The logs for all `puppet infrastructure run` Bolt plans are located at `/var/log/puppetlabs/installer/bolt_info.log`.

CAUTION:

Replacing your certificate authority invalidates all existing certificates in your environment. Complete this task only if and when you're prepared to regenerate certificates for both your infrastructure nodes \(including external PE-PostgreSQL in extra-large installations\) and your entire agent fleet.

### Procedure

1.  On your master logged in as root, run:

    ```
    puppet infrastructure run rebuild_certificate_authority
    ```

    The SSL and cert directories on your CA server are backed up with "\_bak" appended to the end, CA files are removed and certificates are rebuilt, and a Puppet run completes.


### Results

**Note:** To support recovery, backups of your certificates are saved and the location of the backup directory is output to the PE console. If the command fails after deleting the certificates, you can restore your certificates with the contents of this backup directory.

## Regenerate compiler certificates

Regenerate compiler certificates to fix a compromised certificate or troubleshoot SSL errors on compilers, or if you recreated your certificate authority.

### Before you begin

The `puppet infrastructure run` command leverages built-in Bolt plans to automate certain management tasks. To use this command, you must be able to connect using SSH from your master to any nodes that the command modifies. You can establish an SSH connection using key forwarding, a local key file, or by specifying keys in `.ssh/config` on your master. For more information, see [Bolt OpenSSH configuration options](https://puppet.com/docs/bolt/latest/bolt_configuration_options.html#openssh-configuration-options).

### About this task

To view all available parameters, use the `--help` flag. The logs for all `puppet infrastructure run` Bolt plans are located at `/var/log/puppetlabs/installer/bolt_info.log`.

### Procedure

1.  On your master logged in as root, run:

    ```
    puppet infrastructure run regenerate_compiler_certificate target=<COMPILER_HOSTNAME>
    ```

    You can also specify these optional parameters:

    -   `dns_alt_names` – Comma-separated list of alternate DNS names to be added to the certificates generated for your agents.

        **Important:** To use the `dns_alt_names` parameter, you must configure Puppet Server with `allow-subject-alt-names` in the `certificate-authority` section of `ca.conf`. To ensure naming consistency, if your `puppet.conf` file includes a `dns_alt_names` entry, you must include the `dns_alt_names` parameter and pass in all alt names included in the entry when regenerating your compiler certificates.

    -   `tmpdir` — Path to a directory to use for uploading and executing temporary files.

### Results

The compiler's SSL directory is backed up to `/etc/puppetlabs/puppet/ssl_bak`, its certificate is regenerated and signed, a Puppet run completes, and the compiler resumes its role in your deployment.

**Note:** To support recovery, backups of your certificates are saved and the location of the backup directory is output to the PE console. If the command fails after deleting the certificates, you can restore your certificates with the contents of this backup directory.

## Regenerate \*nix agent certificates

Regenerate \*nix agent certificates to fix a compromised certificate or troubleshoot SSL errors on agents, or if you recreated your certificate authority.

### Before you begin

The `puppet infrastructure run` command leverages built-in Bolt plans to automate certain management tasks. To use this command, you must be able to connect using SSH from your master to any nodes that the command modifies. You can establish an SSH connection using key forwarding, a local key file, or by specifying keys in `.ssh/config` on your master. For more information, see [Bolt OpenSSH configuration options](https://puppet.com/docs/bolt/latest/bolt_configuration_options.html#openssh-configuration-options).

### About this task

To view all available parameters, use the `--help` flag. The logs for all `puppet infrastructure run` Bolt plans are located at `/var/log/puppetlabs/installer/bolt_info.log`.

### Procedure

1.  On your master logged in as root, run:

    ```
    puppet infrastructure run regenerate_agent_certificate agent=<AGENT_HOSTNAME>
    ```

    **Important:** If you rebuilt the certificate authority, you must clean up the CRL using the `clean_crl` parameter, for example:

    ```
    puppet infrastructure run regenerate_agent_certificate agent=<AGENT_HOSTNAME> clean_crl=true
    ```

    You can also specify these optional parameters:

    -   `dns_alt_names` – Comma-separated list of alternate DNS names to be added to the certificates generated for your agents.

        **Important:** To use the `dns_alt_names` parameter, you must configure Puppet Server with `allow-subject-alt-names` in the `certificate-authority` section of `ca.conf`. To ensure naming consistency, if your `puppet.conf` file includes a `dns_alt_names` entry, you must include the `dns_alt_names` parameter and pass in all alt names included in the entry when regenerating your agent certificates.

    -   `tmpdir` — Path to a directory to use for uploading and executing temporary files.
    To regenerate certificates for multiple \*nix agents, use a comma-separated list of the agents' hostnames, such as:

    ```
    puppet infrastructure run regenerate_agent_certificate agent=agent1.example.net,agent2.example.net,agent3.example.net
    ```


### Results

The \*nix agent's SSL directory is backed up to `/etc/puppetlabs/puppet/ssl_bak`, its certificate is regenerated and signed, a Puppet run completes, and the agent resumes its role in your deployment.

**Note:** To support recovery, backups of your certificates are saved and the location of the backup directory is output to the PE console. Note that the location shown in the console is relative to your agent's files. If the command fails after deleting the certificates, you can restore your certificates with the contents of this backup directory.

## Regenerate Windows agent certificates

Regenerate Windows agent certificates to fix a compromised certificate or troubleshoot SSL errors on agents, or if you recreated your certificate authority.

### About this task

Unless otherwise indicated, perform these steps on the Windows agent node that you're regenerating certificates for.

### Procedure

1.  If you did not recreate your certificate authority, you must log into your master and clear the cert for the agent node:

    ```
    puppetserver ca clean --certname <CERTNAME>
    ```

2.  On the agent, back up the `%PROGRAMDATA%/PuppetLabs/puppet/etc/ssl` directory.

    If something goes wrong, you might need to restore these directories so your deployment remains functional.

3.  Stop the Puppet agent and PXP agent services.

    ```
    puppet resource service puppet ensure=stopped
    puppet resource service pxp-agent ensure=stopped
    ```

4.  Using the administrator account, delete the agent SSL directory located at `%PROGRAMDATA%/PuppetLabs/puppet/etc/ssl`.

5.  Remove the agent's cached catalog. Use the Administrator confdir to delete `%PROGRAMDATA%\PuppetLabs\puppet\cache\client_data\catalog\<CERNAME>.json`.

6.  Re-start the Puppet agent service: `puppet resource service puppet ensure=running`

    After the agent starts, it automatically generates keys and request a new certificate from the Puppet CA.

7.  If you aren't using autosigning, sign each agent node's certificate request using the console's request manager, or from your master:

    ```
    puppetserver ca list 
    puppetserver ca sign --certname <NAME>
    ```

    **Note:** For more information about autosigning, see [Autosigning certificate requests](https://puppet.com/docs/puppet/6.10/ssl_autosign.html).

8.  From the console or command line, run Puppet on the node.


### Results

The Windows agent performs a full catalog run, restarts the PXP agent service, and resumes its role in your PE deployment.

## Regenerate master certificates

Regenerate master certificates to specify a new DNS alt name or other trusted data. This process regenerates the certificates for all primary infrastructure nodes, including external PE-PostgreSQL in extra-large installations.

### Before you begin

The `puppet infrastructure run` command leverages built-in Bolt plans to automate certain management tasks. To use this command, you must be able to connect using SSH from your master to any nodes that the command modifies. You can establish an SSH connection using key forwarding, a local key file, or by specifying keys in `.ssh/config` on your master. For more information, see [Bolt OpenSSH configuration options](https://puppet.com/docs/bolt/latest/bolt_configuration_options.html#openssh-configuration-options).

### About this task

To view all available parameters, use the `--help` flag. The logs for all `puppet infrastructure run` Bolt plans are located at `/var/log/puppetlabs/installer/bolt_info.log`.

### Procedure

1.  On your master logged in as root, run:

    ```
    puppet infrastructure run regenerate_master_certificate
    ```

    You can specify these optional parameters:

    -   `dns_alt_names` – Comma-separated list of alternate DNS names to be added to the certificates generated for your master.

        **Important:** To use the `dns_alt_names` parameter, you must configure Puppet Server with `allow-subject-alt-names` in the `certificate-authority` section of `ca.conf`. To ensure naming consistency, if your `puppet.conf` file includes a `dns_alt_names` entry, you must include the `dns_alt_names` parameter and pass in all alt names included in the entry when regenerating your master certificate.

    -   `tmpdir` — Path to a directory to use for uploading and executing temporary files.



### Results

The certificates are regenerated for your primary infrastructure nodes.

**Note:** To support recovery, backups of your certificates are saved and the location of the backup directory is output to the PE console. If the command fails after deleting the certificates, you can restore your certificates with the contents of this backup directory.

## Regenerate replica certificates

Regenerate master replica certificates for your high availability installation to specify a new DNS alt name or other trusted data.

### Before you begin

The `puppet infrastructure run` command leverages built-in Bolt plans to automate certain management tasks. To use this command, you must be able to connect using SSH from your master to any nodes that the command modifies. You can establish an SSH connection using key forwarding, a local key file, or by specifying keys in `.ssh/config` on your master. For more information, see [Bolt OpenSSH configuration options](https://puppet.com/docs/bolt/latest/bolt_configuration_options.html#openssh-configuration-options).

### About this task

To view all available parameters, use the `--help` flag. The logs for all `puppet infrastructure run` Bolt plans are located at `/var/log/puppetlabs/installer/bolt_info.log`.

### Procedure

1.  On your master logged in as root, run:

    ```
    puppet infrastructure run regenerate_replica_certificate target=<REPLICA_HOSTNAME>
    ```

    You can specify these optional parameters:

    -   `dns_alt_names` – Comma-separated list of alternate DNS names to be added to the certificates generated for your master.

        **Important:** To use the `dns_alt_names` parameter, you must configure Puppet Server with `allow-subject-alt-names` in the `certificate-authority` section of `ca.conf`. If your `puppet.conf` file includes a `dns_alt_names` entry, you must include the `dns_alt_names` parameter and pass in all alt names included in the entry when regenerating your replica certificate.

    -   `tmpdir` — Path to a directory to use for uploading and executing temporary files.

### Results

The replica's SSL directory is backed up to `/etc/puppetlabs/puppet/ssl_bak`, its certificate is regenerated and signed, a Puppet run completes, and the replica resumes its role in your deployment.

**Note:** To support recovery, backups of your certificates are saved on your replica and the location of the backup directory is output to the PE console. If the command fails after deleting the certificates, you can restore your certificates with the contents of this backup directory.

