---
author: Melissa Amos <melissa.amos@puppet.com\>
---

# Installing Puppet Enterprise

You can install PE using express install, which relies on defaults, text install, where you provide a `pe.conf` file with installation parameters, or using a guided web install. Any of these methods is appropriate for installing infrastructure components on your master.

**Related information**  


[Configuring high availability](configuring_high_availability.md)

[Installing compilers](installing_compile_masters.md#)

[Installing external PostgreSQL](installing_postgresql.md#)

## Download and verify the installation package

PE is distributed in downloadable packages specific to supported operating system versions and architectures. Installation packages include the full installation tarball and a GPG signature \(.asc\) file used to verify authenticity.

### Before you begin

You must have GnuPG installed.

### Procedure

1.  [Download](https://puppet.com/download-puppet-enterprise) the tarball appropriate to your operating system and architecture.

2.  Import the Puppet public key.

    ```
    wget -O - https://downloads.puppetlabs.com/puppet-gpg-signing-key.pub | gpg --import
    ```

3.  Print the fingerprint of the key.

    ```
    gpg --fingerprint 0x7F438280EF8D349F
    ```

    The primary key fingerprint displays: 6F6B 1550 9CF8 E59E 6E46 9F32 7F43 8280 EF8D 349F.

4.  Verify the release signature of the installation package.

    ```
    $ gpg --verify puppet-enterprise-<version>-<platform>.tar.gz.asc
    ```

    The result is similar to:

    ```
    gpg: Signature made Tue 18 Sep 2016 10:05:25 AM PDT using RSA key ID EF8D349F
    gpg: Good signature from "Puppet, Inc. Release Key (Puppet, Inc. Release Key)"
    ```

    **Note:** If you don't have a trusted path to one of the signatures on the release key, you receive a warning that a valid path to the key couldn't be found.


## Install using express install

Express installation relies on default settings to install PE, so you don't have to edit a `pe.conf` file before or during installation. At the end of the installation process, you're prompted to provide a console admininstrator password, which is the only user-required value.

### About this task

Perform these steps logged in as root.

### Procedure

1.  Unpack the installation tarball:

    ```
    tar -xf <TARBALL_FILENAME>              
    ```

2.  From the installer directory, run the installer:

    ```
    sudo ./puppet-enterprise-installer
    ```

3.  When prompted, select express install mode.

4.  After installation completes, follow the prompts to specify a password, or run: `puppet infra console_password --password=<MY_PASSWORD>`

5.  Run Puppet twice: `puppet agent -t`.


### What to do next

You must restart the shell before you can use PE client tool subcommands.

## Install using text install

When you run the installer in text mode, you provide a configuration file \(`pe.conf`\) to the installer. The `pe.conf` file contains values for the parameters needed for installation.

### About this task

Perform these steps logged in as root.

### Procedure

1.  Unpack the installation tarball:

    ```
    tar -xf <TARBALL_FILENAME>              
    ```

2.  From the installer directory, run the installer. The installation steps vary depending on the method you choose.

    -   To use a `pe.conf` file that you've previously populated, run the installer **with the `-c` flag** pointed at the `pe.conf` file.:

        ```
        sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>
        ```

    -   To have the installer open a copy of `pe.conf` for you to edit and install with, run the installer **without the `-c` flag**:

        ```
        sudo ./puppet-enterprise-installer
        ```

        1.  Select **text-mode** when prompted.

        2.  Specify required installation parameters. If you're installing with specialized configuration, such as external PostgreSQL or an independent intermediate certificate authority, add parameters required for those configurations.

        3.  Save and close the file. Installation begins.

3.  After installation completes, if you didn't specify a console admin password in your `pe.conf` file, follow the prompts to specify a password, or run: `puppet infra console_password --password=<MY_PASSWORD>`

4.  Run Puppet twice: `puppet agent -t`.


### What to do next

You must restart the shell before you can use PE client tool subcommands. 

**Related information**  


[Use an independent intermediate certificate authority](use_an_independent_intermediate_ca.md)

[Text install options](installing_pe.md#)

[Configuration parameters and the pe.conf file](installing_pe.md#)

[External PostgreSQL parameters](installing_pe.md#)

[Executable binaries and symlinks installed](what_gets_installed_and_where.md#)

### Text install options

When you run the installer in text mode, you can use the `-c` option to specify the full path to an existing `pe.conf` file. You can pair these additional options with the `-c` option.

|Option|Definition|
|------|----------|
|`-D`|Display debugging information|
|`-q`|Run in quiet mode. The installation process isn't displayed. If errors occur during the installation, the command quits with an error message.|
|`-y`|Run automatically using the `pe.conf` file at `/etc/puppetlabs/enterprise/conf.d/`. If the file is not present or is invalid, installation or upgrade fails.|
|`-V`|Display verbose debugging information.|
|`-h`|Display help information.|
|`force`|For upgrades only, bypass PostgreSQL migration validation. This option must appear last, after the end-of-options signifier \(`--`\), for example `sudo ./puppet-enterprise-installer -c pe.conf -- --force`|

## Install using web install

Web-based installation uses a web server to guide you through installation.

### Before you begin

Review the [Web install prerequisites](installing_pe.md#).

### Procedure

1.  If necessary, forward ports to the web-based installer.

    The web-based installer requires access to port 3000 on the machine you're running the installer from. If you can't connect directly to port 3000 — for example, if you're installing PE on a virtual machine or have firewall rules that prevent direct access — you can port forward, or "tunnel," to the installer using SSH.

    -   \*nix

        ```
        ssh -L 3000:localhost:3000 jumphost.exmple.tld
        ```

    -   Windows
        1.  Open PuTTY, select **Sessions** and in the **Host Name** field, enter the FQDN of the host you want to run the installer from.

        2.  Select **Tunnels** and in the **Source Port** field, enter 3000.

        3.  In the **Destination** field, enter localhost:3000.

        4.  Select **Local**, click **Add**, and then click **Open**. 

2.  Unpack the installation tarball:

    ```
    tar -xf <TARBALL_FILENAME>              
    ```

3.  From the installer directory, run the installer:

    ```
    sudo ./puppet-enterprise-installer
    ```

4.  When prompted, choose guided installation

    The installer starts a web server and provides an installer URL.

    The default installer URL is `https://<INSTALL_PLATFORM_HOSTNAME>:3000`. If you forwarded ports in step 1, the URL is `https://localhost:3000`.

5.  In a browser, access the installer URL and accept the security request.

    The installer uses a default SSL certificate. You must add a security exception in order to access the installer.

    **Important:** Leave your terminal connection open until the installation is complete or else installation fails.

6.  Follow the prompts to configure your installation.

7.  On the validation page, verify the configuration and, if there aren't any outstanding issues, click **Deploy now**.

    Installation begins. You can monitor the installation as it runs by toggling **Log View** and **Summary View**. If you notice errors, check `/var/log/puppetlabs/installer/install_log.lastrun.<hostname>.log` on the machine from which you're running the installer.

    When the installation completes, the installer script that was running in the terminal closes.

8.  Click **Start using Puppet Enterprise** to log into the console.


### Results

**Related information**  


[Web install prerequisites](installing_pe.md#)

[Web install options](installing_pe.md#)

[Installing external PostgreSQL](installing_postgresql.md#)

[Executable binaries and symlinks installed](what_gets_installed_and_where.md#)

### Web install prerequisites

Review these prerequisites and tips before beginning a web-based installation.

-   If you've previously installed Puppet or Puppet Enterprise, make sure that the machine you're installing on is free of any artifacts left over from the previous installation.

-   Make sure that DNS is properly configured on the machines you're installing on.

-   All nodes must know their own hostnames, which you can achieve by properly configuring reverse DNS on your local DNS server, or by setting the hostname explicitly. Setting the hostname usually involves the `hostname` command and one or more configuration files, but the exact method varies by platform.

-   All nodes must be able to reach each other by name, which you can achieve with a local DNS server, or by editing the `/etc/hosts` file on each node to point to the proper IP addresses.

-   You can run the installer from a machine that is part of your deployment or from a machine that is outside your deployment.

-   The machine you run the installer from must have the same operating system and architecture as your deployment.

-   The web-based installer does not support sudo configurations with `Defaults targetpw` or `Defaults rootpw`. Make sure your `/etc/sudoers` file does not contain, or comment out, those lines.

-   For Debian users, if you gave the root account a password during installation of Debian, sudo may not have been installed. In this case, you must either install as root, or install sudo on any nodes on which you want to install.


#### SSH prerequisites

SSH requirements very depending on your installation method.

You don't need to take additional steps to configure SSH if you:

-   Choose **Install on this server** during installation.
-   Have a properly configured SSH agent with agent forwarding enabled.

If you're using SSH keys to authenticate across nodes, the public key for the user account performing the installation must be included in the `authorized_keys` file for that user account on each infrastructure node, including the machine from which you're running the installer. This requirement applies to root or non-root users.

|Installation method|Requirements|Prerequisites|
|-------------------|------------|-------------|
|Root with a password|The installer requires the username and password for each infrastructure node.|Remote root SSH login must be enabled on each infrastructure node, including the node from which you're running the installer.|
|Non-root with a password|Sudo must be enabled for the non-root user on each infrastructure node.|
|Root with an SSH key|The installer requires the username, private key path, and key passphrase \(as needed\) for each infrastructure node.|-   Remote root SSH login must enabled on each node, including the node from which you're running the installer.

-   The public root ssh key must be added to `authorized_keys` on each infrastructure node.


|
|Non-root with an SSH key|-   The non-root user must be granted sudo access on each infrastructure node.

-   The non-root user SSH key must be added to `authorized_keys` on each infrastructure node.


|

### Web install options

Use this reference when providing values in the web-based installer.

SSH details are required only if you opt to install on another server. 

|Setting|Value|
|-------|-----|
|**Puppet master FQDN**|Fully qualified domain name of the server you're installing on. This FQDN is used as the name of the master certificate. This FQDN must be resolvable from the machine on which you're running the installer. 

 To ensure you're using the proper FQDN for the master, run `sudo /opt/puppetlabs/bin/puppet config print certname` when the installation completes. If the installation fails because the FQDN value is incorrect, run the `config print certname` command and re-run the installer with the correct value.

|
|**DNS altnames**|Comma-separated list of static, valid, DNS altnames so agents can trust the master. Make sure that this static list contains the DNS name or alias you’re be configuring your agents to contact. The default settings include `puppet`.|
|**SSH username**|Username to use when connecting to the master. This user must either be root or have sudo access. The default value is `root`.|
|**SSH password**|Password associated with the SSH username. This password is used only if the user requires a password for sudo access.|
|**SSH key file path**|Absolute path to the SSH key on the machine you're performing the installation from. This value is used if an SSH password is not specified. Defaults to the root SSH key path.|
|**SSH key passphrase**|Passphrase for the SSH key, if applicable.|

## Configuration parameters and the `pe.conf` file

A `pe.conf` file is a HOCON formatted file that declares parameters and values needed to install, upgrade, and configure PE.

You can create or obtain a `pe.conf` file by:

-   Using the example `pe.conf` file provided in the `conf.d` directory in the installer tarball.

    **Tip:** In most cases, you can use the example `pe.conf` file without making any changes.

-   Selecting the text-mode installation option when prompted by the installer. This option opens your default text editor with the example `pe.conf` file, which you can modify as needed. Installation proceeds using that `pe.conf` after you quit the editor.
-   Using the web-based installer to create a `pe.conf` file. After you run the web-based installer, you can find the file at `/etc/puppetlabs/enterprise/conf.d`. You can also download the file by following the link provided on the confirmation page of the web-based installer.

The following are examples of valid paramater and value expressions:

|Type|Value|
|----|-----|
|FQDNs|`"puppet_enterprise::puppet_master_host": "master.example.com"`|
|Strings|`"console_admin_password": "mypassword"`|
|Arrays|`[ "puppet", "puppetlb-01.example.com" ]`|
|Booleans|`"puppet_enterprise::profile::orchestrator::run_service": true` Valid Boolean values are `true` or `false` \(case sensitive, no quotation marks\).

 **Note:** Don't use Yes \(y\), No \(n\), 1, or 0.

|
|JSON hashes|`"puppet_enterprise::profile::orchestrator::java_args": {"Xmx": "256m", "Xms": "256m"}`|
|Integer|`"puppet_enterprise::profile::console::rbac_session_timeout": "60"`|

**Important:** Don't use single quotes on parameter values. Use double quotes as shown in the examples.

### Installation parameters

These parameters are required for installation.

**Tip:** To simplify installation, you can keep the default value of `%{::trusted.certname}` for your master and provide a console administrator password after running the installer.

-   **`puppet_enterprise::puppet_master_host`**

    The FQDN of the node hosting the master, for example `master.example.com`.

    Default: `%{::trusted.certname}`


### Database configuration parameters

These are the default parameters and values supplied for the PE databases.

This list is intended for reference only; don't change or customize these parameters.

-   **`puppet_enterprise::activity_database_name`**

    Name for the activity database.

    Default: `pe-activity`

-   **`puppet_enterprise::activity_database_read_user`**

    Activity database user that can perform only read functions.

    Default: `pe-activity-read`

-   **`puppet_enterprise::activity_database_write_user`**

    Activity database user that can perform only read and write functions.

    Default: `pe-activity-write`

-   **`puppet_enterprise::activity_database_super_user`**

    Activity database superuser.

    Default: `pe-activity`

-   **`puppet_enterprise::activity_service_migration_db_user`**

    Activity service database user used for migrations.

    Default: `pe-activity`

-   **`puppet_enterprise::activity_service_regular_db_user`**

    Activity service database user used for normal operations.

    Default: `pe-activity-write`

-   **`puppet_enterprise::classifier_database_name`**

    Name for the classifier database.

    Default: `pe-classifier`

-   **`puppet_enterprise::classifier_database_read_user`**

    Classifier database user that can perform only read functions.

    Default: `pe-classifier-read`

-   **`puppet_enterprise::classifier_database_write_user`**

    Classifier database user that can perform only read and write functions.

    `pe-classifier-write`

-   **`puppet_enterprise::classifier_database_super_user`**

    Classifier database superuser.

    `pe-classifier`

-   **`puppet_enterprise::classifier_service_migration_db_user`**

    Classifier service user used for migrations.

    Default: `pe-classifier`

-   **`puppet_enterprise::classifier_service_regular_db_user`**

    Classifier service user used for normal operations.

    Default: `pe-classifier-write`

-   **`puppet_enterprise::orchestrator_database_name`**

    Name for the orchestrator database.

    Default: `pe-orchestrator`

-   **`puppet_enterprise::orchestrator_database_read_user`**

    Orchestrator database user that can perform only read functions.

    Default: `pe-orchestrator-read`

-   **`puppet_enterprise::orchestrator_database_write_user`**

    Orchestrator database user that can perform only read and write functions.

    Default: `pe-orchestrator-write`

-   **`puppet_enterprise::orchestrator_database_super_user`**

    Orchestrator database superuser.

    Default: `pe-orchestrator`

-   **`puppet_enterprise::orchestrator_service_migration_db_user`**

    Orchestrator service user used for migrations.

    Default: `pe-orchestrator`

-   **`puppet_enterprise::orchestrator_service_regular_db_user`**

    Orchestrator service user used for normal operations.

    Default: `pe-orchestrator-write`

-   **`puppet_enterprise::puppetdb_database_name`**

    Name for the PuppetDB database.

    Default: `pe-puppetdb`

-   **`puppet_enterprise::rbac_database_name`**

    Name for the RBAC database.

    Default: `pe-rbac`

-   **`puppet_enterprise::rbac_database_read_user`**

    RBAC database user that can perform only read functions.

    Default: `pe-rbac-read`

-   **`puppet_enterprise::rbac_database_write_user`**

    RBAC database user that can perform only read and write functions.

    Default: `pe-rbac-write`

-   **`puppet_enterprise::rbac_database_super_user`**

    RBAC database superuser.

    Default: `pe-rbac`

-   **`puppet_enterprise::rbac_service_migration_db_user`**

    RBAC service user used for migrations.

    `pe-rbac`

-   **`puppet_enterprise::rbac_service_regular_db_user`**

    RBAC service user used for normal operations.

    Default: `pe-rbac-write`


### External PostgreSQL parameters

These parameters are required to install an external PostgreSQL instance. Password parameters can be added to standard installations if needed.

-   **`puppet_enterprise::database_host`**

    Agent certname of the node hosting the database component. Don't use an alt name for this value.

-   **`puppet_enterprise::database_port`**

    The port that the database is running on.

    Default: `5432`

-   **`puppet_enterprise::database_ssl`**

    `true` or `false`. For unmanaged PostgreSQL installations don't use SSL security, set this parameter to `false`.

    Default: `true`

-   **`puppet_enterprise::database_cert_auth`**

    `true` or `false`.

    **Important:** For unmanaged PostgreSQL installations don't use SSL security, set this parameter to `false`.

    Default: `true`

-   **`puppet_enterprise::puppetdb_database_password`**

    Password for the PuppetDB database user. Must be a string, such as `"mypassword"`.

-   **`puppet_enterprise::classifier_database_password`**

    Password for the classifier database user. Must be a string, such as `"mypassword"`.

-   **`puppet_enterprise::classifier_service_regular_db_user`**

    Database user the classifier service uses for normal operations.

    Default: `pe-classifier`

-   **`puppet_enterprise::classifier_service_migration_db_user`**

    Database user the classifier service uses for migrations.

    Default: `pe-classifier`

-   **`puppet_enterprise::activity_database_password`**

    Password for the activity database user. Must be a string, such as `"mypassword"`.

-   **`puppet_enterprise::activity_service_regular_db_user`**

    Database user the activity service uses for normal operations.

    Default: `"pe-activity"`

-   **`puppet_enterprise::activity_service_migration_db_user`**

    Database user the activity service uses for migrations.

    Default: `pe-activity"`

-   **`puppet_enterprise::rbac_database_password`**

    Password for the RBAC database user. Must be a string, such as `"mypassword"`.

-   **`puppet_enterprise::rbac_service_regular_db_user`**

    Database user the RBAC service uses for normal operations.

    Default: `"pe-rbac"`

-   **`puppet_enterprise::rbac_service_migration_db_user`**

    Database user the RBAC service uses for migrations.

    Default: `"pe-rbac"`

-   **`puppet_enterprise::orchestrator_database_password`**

    Password for the orchestrator database user. Must be a string, such as `"mypassword"`.

-   **`puppet_enterprise::orchestrator_service_regular_db_user`**

    Database user the orchestrator service uses for normal operations.

    Default: `pe-orchestrator`

-   **`puppet_enterprise::orchestrator_service_migration_db_user`**

    Database user the orchestrator service uses for migrations.

    Default: `"pe-orchestrator"`


### Master parameters

Use these parameters to configure and tune the master.

-   **`pe_install::puppet_master_dnsaltnames`**

    An array of strings that represent the DNS altnames to be added to the SSL certificate generated for the master.

    Default: `["puppet"]`

-   **`puppet_enterprise::profile::certificate_authority`**

    Array of additional certificates to be allowed access to the `/certificate_statusAPI` endpoint. This list is added to the base certificate list.

-   **`puppet_enterprise::profile::master::code_manager_auto_configure`**

    `true` to automatically configure the Code Manager service, or `false`.

-   **`puppet_enterprise::profile::master::r10k_remote`**

    String that represents the Git URL to be passed to the `r10k.yaml` file, for example `"git@your.git.server.com:puppet/control.git"`. The URL can be any URL that's supported by r10k and Git. This parameter is required only if you want r10k configured when PE is installed; it must be specified in conjunction with `puppet_enterprise::profile::master::r10k_private_key`.

-   **`puppet_enterprise::profile::master::r10k_private_key`**

    String that represents the local file system path on the master where the SSH private key can be found and used by r10k, for example `"/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa"`. This parameter is required only if you want r10k configured when PE is installed; it must be specified in conjunction with `puppet_enterprise::profile::master::r10k_remote`.

-   **`puppet_enterprise::profile::master::check_for_updates`**

    `true` to check for updates whenever the pe-puppetserver service restarts, or `false`.

    Default: `true`


### Console and console-services parameters

Use these parameters to customize the behavior of the console and console-services. Parameters that begin with `puppet_enterprise::profile` can be modified from the console itself. See the configuration methods documents for more information on how to change parameters in the console or Hiera.

-   **`puppet_enterprise::profile::console::classifier_synchronization_period`**

    Integer representing, in seconds, the classifier synchronization period, which controls how long it takes the node classifier to retrieve classes from the master.

    Default: `"600"` \(seconds\).

-   **`puppet_enterprise::profile::console::rbac_failed_attempts_lockout`**

    Integer specifying how many failed login attempts are allowed on an account before that account is revoked.

    Default: `"10"` \(attempts\).

-   **`puppet_enterprise::profile::console::rbac_password_reset_expiration`**

    Integer representing, in hours, how long a user's generated token is valid for. An administrator generates this token for a user so that they can reset their password.

    Default: `"24"` \(hours\).

-   **`puppet_enterprise::profile::console::rbac_session_timeout`**

    Integer representing, in minutes, how long a user's session may last. The session length is the same for node classification, RBAC, and the console.

    Default: `"60"` \(minutes\).

-   **`puppet_enterprise::profile::console::session_maximum_lifetime`**

    Integer representing the maximum allowable period that a console session may be valid. May be set to "0" to not expire before the maximum token lifetime.

    Supported units are "s" \(seconds\), "m" \(minutes\), "h" \(hours\), "d" \(days\), "y" \(years\). Units are specified as a single letter following an integer, for example "1d"\(1 day\). If no units are specified, the integer is treated as seconds.

-   **`puppet_enterprise::profile::console::console_ssl_listen_port`**

    Integer representing the port that the console is available on.

    Default: `[443]`

-   **`puppet_enterprise::profile::console::ssl_listen_address`**

    Nginx listen address for the console.

    Default: `"0.0.0.0"`

-   **`puppet_enterprise::profile::console::classifier_prune_threshold`**

    Integer representing the number of days to wait before pruning the size of the classifier database. If you set the value to `"0"`, the node classifier service is never pruned.

-   **`puppet_enterprise::profile::console::classifier_node_check_in_storage`**

    `"true"` to store an explanation of how nodes match each group they're classified into, or `"false"`.

    Default: `"false"`

-   **`puppet_enterprise::profile::console::display_local_time`**

    `"true"` to display timestamps in local time, with hover text showing UTC time, or `"false"` to show timestamps in UTC time.

    Default: `"false"`


Modify these configuration parameters in Hiera or `pe.conf`, not the console:

-   **`puppet_enterprise::api_port`**

    SSL port that the node classifier is served on.

    Default: `[4433]`

-   **`puppet_enterprise::console_services::no_longer_reporting_cutoff`**

    Length of time, in seconds, before a node is considered unresponsive.

    Default: `3600` \(seconds\)


-   **`console_admin_password`**

    The password to log into the console, for example `"myconsolepassword"`.

    Default: Specified during installation.


### Orchestrator and orchestration services parameters

Use these parameters to configure and tune the orchestrator and orchestration services.

-   **`puppet_enterprise::profile::agent::pxp_enabled`**

    `true` to enable the Puppet Execution Protocol service, which is required to use the orchestrator and run Puppet from the console, or `false`.

    Default: `true`

-   **`puppet_enterprise::profile::bolt_server::concurrency`**

    An integer that determines the maximum number of concurrent requests orchestrator can make to bolt-server.

    CAUTION:

    Do not set a concurrency limit that is higher than the bolt-server limit. This can cause timeouts that lead to failed task runs.

    Default: The default value is set to the current value stored for bolt-server.

-   **`puppet_enterprise::profile::orchestrator::global_concurrent_compiles`**

    Integer representing how many concurrent compile requests can be outstanding to the master, across all orchestrator jobs.

    Default: `"8"` requests

-   **`puppet_enterprise::profile::orchestrator::job_prune_threshold`**

    Integer representing the days after which job reports should be removed.

    Default: `"30"` days

-   **`puppet_enterprise::profile::orchestrator::pcp_timeout`**

    Integer representing the length of time, in seconds, before timeout when agents attempt to connect to the Puppet Communications Protocol broker in a Puppet run triggered by the orchestrator.

    Default: `"30"` seconds

-   **`puppet_enterprise::profile::orchestrator::run_service`**

    `true` to enable orchestration services, or `false`.

    Default: `true`

-   **`puppet_enterprise::profile::orchestrator::task_concurrency`**

    Integer representing the number of tasks that can run at the same time.

    Default: `"250"` tasks

-   **`puppet_enterprise::profile::orchestrator::use_application_services`**

    `true` to enable application management, or `false`.

    Default: `false`

-   **`puppet_enterprise::pxp_agent::ping_interval`**

    Integer representing the interval, in seconds, between agents' attempts to ping Puppet Communications Protocol brokers.

    Default: `"120"` seconds

-   **`puppet_enterprise::pxp_agent::pxp_logfile`**

    String representing the path to the Puppet Execution Protocol agent log file. Change as needed.

    Default: `/var/log/puppetlabs/pxp-agent/pxp-agent.log` \(\*nix\) or `C:/ProgramData/PuppetLabs/pxp-agent/var/log/pxp-agent.log` \(Windows\)


**Related information**  


[Configuring Puppet orchestrator](configuring_puppet_orchestrator.md#)

### PuppetDB parameters

Use these parameters to configure and tune PuppetDB.

-   **`puppet_enterprise::puppetdb::command_processing_threads`**

    Integer representing how many command processing threads PuppetDB uses to sort incoming data. Each thread can process a single command at a time.

    Default: Half the number of cores in your system, for example `"8"`.

-   **`puppet_enterprise::profile::master::puppetdb_report_processor_ensure`**

    `present` to generate agent run reports and submit them to PuppetDB, or `absent`

    Default: `present`

-   **`puppet_enterprise::puppetdb_port`**

    Integer in brackets representing the SSL port that PuppetDB listens on.

    Default: `"[8081]"`

-   **`puppet_enterprise::profile::puppetdb::node_purge_ttl`**

    “Time-to-live” value before deactivated or expired nodes are deleted, along with all facts, catalogs, and reports for the node. For example, a value of `"14d"`  sets the time-to-live to 14 days.

    Default: `"14d"`


### Java parameters

Use these parameters to configure and tune Java.

-   **`puppet_enterprise::profile::master::java_args`**

    JVM \(Java Virtual Machine\) memory, specified as a JSON hash, that is allocated to the Puppet Server service, for example `{"Xmx": "4096m", "Xms": "4096m"}`.

-   **`puppet_enterprise::profile::puppetdb::java_args`**

    JVM memory, specified as a JSON hash, that is allocated to the PuppetDB service, for example `{"Xmx": "512m", "Xms": "512m"}`.

-   **`puppet_enterprise::profile::console::java_args`**

    JVM memory, specified as a JSON hash, that is allocated to console services, for example `{"Xmx": "512m", "Xms": "512m"}`.

-   **`puppet_enterprise::profile::orchestrator::java_args`**

    JVM memory, set as a JSON hash, that is allocated to orchestration services, for example, `{"Xmx": "256m", "Xms": "256m"}`.


