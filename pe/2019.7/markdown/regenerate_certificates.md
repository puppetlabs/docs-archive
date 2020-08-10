# Regenerate infrastructure certificates

Regenerating certificates and security credentials—both private and public keys—created by the built-in PE certificate authority can help ensure the security of your installation in certain cases.

The process for regenerating certificates varies depending on your goal.

|If your goal is to...|Do this...|
|---------------------|----------|
|Upgrade to the intermediate certificate architecture introduced in Puppet 6.0.|Complete these tasks in order:

1.  [Delete and recreate the certificate authority](regenerate_certificates.md#)

2.  [Regenerate compiler certificates](regenerate_certificates.md#), if applicable

3.  [Regenerate agent certificates](regenerate_certificates.md#)

4.  [Regenerate replica certificates](regenerate_certificates.md#)

|
|Fix a compromised or damaged certificate authority.|
|Fix a compromised compiler certificate or troubleshoot SSL errors on compilers.|[Regenerate compiler certificates](regenerate_certificates.md#)|
|Fix a compromised agent certificate or troubleshoot SSL errors on agent nodes.|[Regenerate agent certificates](regenerate_certificates.md#) or [Regenerate Windows agent certificates](regenerate_windows_agent_certificates.md)|
|Specify a new DNS alt name or other trusted data.|[Regenerate master certificates](regenerate_certificates.md#)|

**Note:** To support recovery, backups of your certificates are saved and the location of the backup directory is output to the console. If the command fails after deleting the certificates, you can restore your certificates with the contents of this backup directory.

## Delete and recreate the certificate authority

Recreate the certificate authority only if you're upgrading to the new certificate architecture introduced in Puppet 6.0, or if your certificate authority was compromised or damaged beyond repair.

The `puppet infrastructure run` command leverages built-in Bolt plans to automate certain management tasks. To use this command, you must be able to connect using SSH from your master to any nodes that the command modifies. You can establish an SSH connection using key forwarding, a local key file, or by specifying keys in `.ssh/config` on your master. For more information, see [Bolt OpenSSH configuration options](https://puppet.com/docs/bolt/latest/bolt_configuration_options.html#openssh-configuration-options).

CAUTION:

Replacing your certificate authority invalidates all existing certificates in your environment. Complete this task only if and when you're prepared to regenerate certificates for both your infrastructure nodes \(including external PE-PostgreSQL in extra-large installations\) and your entire agent fleet.

1.  On your master logged in as root, run:

    ```
    puppet infrastructure run rebuild_certificate_authority
    ```


The SSL and cert directories on your CA server are backed up with "\_bak" appended to the end, CA files are removed and certificates are rebuilt, and a Puppet run completes.

## Regenerate compiler certificates

Regenerate compiler certificates to fix a compromised certificate or troubleshoot SSL errors on compilers, or if you recreated your certificate authority.

1.  On your master logged in as root, run the following command, specifying any additional parameters required for your environment and use case:

    ```
    puppet infrastructure run regenerate_compiler_certificate target=<COMPILER_HOSTNAME>
    ```

    -   **If you use DNS alternative names**, specify `dns_alt_names` as a comma-separated list of names to add to agent certificates.

        **Important:** To ensure naming consistency, if your `puppet.conf` file includes a `dns_alt_names` entry, you must include the `dns_alt_names` parameter and pass in all alternative names included in the entry when regenerating your agent certificates.

    -   **If you recreated your certificate authority**, or are otherwise unable to connect to the compiler with the orchestrator, specify `clean_crl=true` and `--use-ssh`, as well as any additional parameters needed to connect over SSH.

The compiler's SSL directory is backed up to `/etc/puppetlabs/puppet/ssl_bak`, its certificate is regenerated and signed, a Puppet run completes, and the compiler resumes its role in your deployment.

## Regenerate agent certificates

Regenerate \*nix or Windows agent certificates to fix a compromised certificate or troubleshoot SSL errors on agents, or if you recreated your certificate authority.

1.  On your master logged in as root, run the following command, specifying any additional parameters required for your environment and use case:

    ```
    puppet infrastructure run regenerate_agent_certificate agent=<AGENT_HOSTNAME_1>,<AGENT_HOSTNAME_2>
    ```

    -   **If you use DNS alternative names**, specify `dns_alt_names` as a comma-separated list of names to add to agent certificates.

        **Important:** To ensure naming consistency, if your `puppet.conf` file includes a `dns_alt_names` entry, you must include the `dns_alt_names` parameter and pass in all alternative names included in the entry when regenerating your agent certificates.

    -   **If you recreated your certificate authority**, or are otherwise unable to connect to nodes with the orchestrator, specify `clean_crl=true` and `--use-ssh`, as well as any additional parameters needed to connect over SSH.

Agent SSL directories are backed up to `/etc/puppetlabs/puppet/ssl_bak` \(\*nix\) or `C:/ProgramData/PuppetLabs/puppet/etc/ssl_bak` \(Windows\), their certificates are regenerated and signed, a Puppet run completes, and the agents resume their role in your deployment.

## Regenerate replica certificates

Regenerate master replica certificates for your high availability installation to specify a new DNS alt name or other trusted data, or if you recreated your certificate authority.

1.  On your master logged in as root, run the following command, specifying any additional parameters required for your environment and use case:

    ```
    puppet infrastructure run regenerate_replica_certificate target=<REPLICA_HOSTNAME>
    ```

    -   **If you use DNS alternative names**, specify `dns_alt_names` as a comma-separated list of names to add to agent certificates.

        **Important:** To ensure naming consistency, if your `puppet.conf` file includes a `dns_alt_names` entry, you must include the `dns_alt_names` parameter and pass in all alternative names included in the entry when regenerating your agent certificates.

    -   **If you recreated your certificate authority**, or are otherwise unable to connect to the replica with the orchestrator, specify `clean_crl=true` and `--use-ssh`, as well as any additional parameters needed to connect over SSH.

The replica's SSL directory is backed up to `/etc/puppetlabs/puppet/ssl_bak`, its certificate is regenerated and signed, a Puppet run completes, and the replica resumes its role in your deployment.

## Regenerate master certificates

Regenerate master certificates to specify a new DNS alt name or other trusted data. This process regenerates the certificates for all primary infrastructure nodes, including external PE-PostgreSQL in extra-large installations.

The `puppet infrastructure run` command leverages built-in Bolt plans to automate certain management tasks. To use this command, you must be able to connect using SSH from your master to any nodes that the command modifies. You can establish an SSH connection using key forwarding, a local key file, or by specifying keys in `.ssh/config` on your master. For more information, see [Bolt OpenSSH configuration options](https://puppet.com/docs/bolt/latest/bolt_configuration_options.html#openssh-configuration-options).

To view all available parameters, use the `--help` flag. The logs for all `puppet infrastructure run` Bolt plans are located at `/var/log/puppetlabs/installer/bolt_info.log`.

1.  On your master logged in as root, run:

    ```
    puppet infrastructure run regenerate_master_certificate
    ```

    You can specify these optional parameters:

    -   `dns_alt_names` – Comma-separated list of alternate DNS names to be added to the certificates generated for your master.

        **Important:** To ensure naming consistency, if your `puppet.conf` file includes a `dns_alt_names` entry, you must include the `dns_alt_names` parameter and pass in all alternative names included in the entry when regenerating your master certificate.

    -   `tmpdir` — Path to a directory to use for uploading and executing temporary files.



