---
layout: default
title: "Web-based installation: split"
canonical: "/pe/latest/install_pe_split.html"
---

The following instructions are for installing a split installation of PE. When you perform a split installation of PE, the Puppet master, the PE console, and PuppetDB components are installed on three separate machines---one machine for each component. 

See [Choosing a PE architecture](./install_system_requirements.html#choosing-a-pe-architecture) for more information about installation types.

## Before beginning your installation

Review the general and SSH prerequisites and notes. 
 
### General prerequisites and notes

- If you've previously installed Puppet or PE, make sure that the machines you're installing PE on are totally free of any artifacts left over from the previous installation.

- Make sure that DNS is properly configured on the machines you're installing PE on. All nodes must **know their own hostnames.** This can be done by properly configuring reverse DNS on your local DNS server or by setting the hostname explicitly. Setting the hostname usually involves the `hostname` command and one or more configuration files, but the exact method varies by platform. In addition, all nodes must be able to **reach each other by name.** This can be done with a local DNS server or by editing the `/etc/hosts` file on each node to point to the proper IP addresses.

- You can run the installer from a machine that is part of your PE deployment or from a machine that is outside your deployment. If you want to run the installer from a machine that is part of your deployment, we recommend you run it from the same node assigned to the Puppet master component (in a split install).

- The machine you run the installer from must have the same OS/architecture as your PE deployment.

- Please ensure that port 3000 is reachable, as the web-based installer uses this port. You can close this port when the installation is complete.

- The web-based installer does not support sudo configurations with `Defaults targetpw` or `Defaults rootpw`. Make sure your `/etc/sudoers` file does not contain, or else comment out, those lines.

- **For Debian users**: If you gave the root account a password during the installation of Debian, sudo may not have been installed. In this case, you will need to either install PE as root, or install sudo on any node(s) on which you want to install PE.

- **A note about passwords**: In some cases, during the installation process, you'll be asked to supply passwords. The `'` (single quote) is forbidden in all passwords.

- If you are using an external PostgreSQL instance that is not managed by PE, review the [External PostgreSQL Option and Prep Notes](./install_system_requirements.html#external-postgresql-option-and-prep-notes) before installing.

### SSH prerequisites and notes

> **Note**: If you plan on choosing **Install on this server** during the installation process, you do not need to take any additional steps to configure SSH.

- If you have a properly configured SSH agent with agent forwarding enabled, you don't need to perform any additional SSH configurations. Your SSH agent will be used by the installer.

- If you're using SSH keys to authenticate across the nodes of your PE installation, the public key for the user account performing the installation must be included in the `authorized_keys` file for that user account on each node that you're installing a PE component on, including the machine from which you're running the installer. This applies to root or non-root users.

- The web-based installer will prompt for the user account name, the SSH private key location, and the SSH passphrase for each node on which you're installing a PE component.

- Please review the following authentication options:

   - **Are you installing using root with a password?** The installer will ask you to provide the username and password for each node on which you're installing a PE component.
      * **Prerequisite**: Remote root SSH login must be enabled on each node, including the node from which you're running the installer.

   - **Are you installing using a non-root user with a password?** The installer will ask you to provide the username and password for each node on which you're installing a PE component.
      * **Prerequisite**: Sudo must be enabled for the non-root user on which you're installing a PE component.

   - **Are you installing using root with an SSH key?** The installer will ask you to provide the username, private key path, and key passphrase (as needed) for each node on which you're installing a PE component.
      * **Prerequisite**: Remote root SSH login must enabled on each node, including the node from which you're running the installer. And the public root SSH key must be added to `authorized_keys` on each node on which you're installing a PE component.

   - **Are you installing using a non-root user with an SSH key?** The installer will ask you to provide the username, private key path, and key passphrase (as needed) for each node on which you're installing a PE component.
      * **Prerequisite**: The non-root user SSH key must be added to `authorized_keys` on each node on which you're installing a PE component. And the non-root user must be granted sudo access on each box.

## Split installation: Step 1

1. [Download and verify the appropriate PE tarball](./install_basic.html#downloading-puppet-enterprise).
2. Unpack the tarball. Run `tar -xf <tarball>`. (Note that you need about 1 GB of space in `/tmp` to untar the installer.) 
3. From the installer directory, run the installer with `sudo ./puppet-enterprise-installer`.
4. When prompted, choose the “Guided” installation option.

   At this point, the PE installer will start a web server and provide a web address: `https://<install platform hostname>:3000`. Please ensure that port 3000 is reachable. If necessary, you can close port 3000 when the installation is complete. Also be sure to use `https`.

5. Copy the address into your browser and continue on to Split Installation: Step 2.

>**Warning**: Leave your terminal connection open until the installation is complete; otherwise, the installation will fail.

## Split installation: Step 2

1. When prompted, accept the security request in your browser.

   The web-based installation uses a default SSL certificate; you’ll have to add a security exception in order to access the web-based installer. This is safe to do.

   You'll be taken to the installer start page.

2. On the start page, click **Let's get started**.
3. Next, you'll be asked to choose your deployment type. Select **Split**.
4. Choose whether you want to install the Puppet master component on the server you're running the installer from or on another server. The choices are **Install on this server** or **Install on another server**.
5. Select whether or not to enable **Application Orchestration** (enabled by default).

   Enabling this feature gives you the Puppet Application Orchestration service, which includes command line tooling (puppet job and puppet app) and application language enhancements. If you have applications modeled in Puppet DSL, the Puppet orchestrator will run nodes in order of their dependencies as specified in your application models. In addition the Puppet orchestrator allows you to run Puppet across all the nodes in a Puppet environment.

   See the [application orchestration documentation](./app_orchestration_overview.html) to learn more about using the orchestrator and modeling applications in Puppet DSL.
   
6. Provide the following information about the Puppet master server:

   > Note that if you selected **Install on this server**, you will only be prompted for steps a - b.

   a. **Puppet master FQDN**: provide the fully qualified domain name of the server you're installing the Puppet master on. It will be the name of the Puppet master certificate.

   b. **DNS aliases**: provide a comma-separated list of static, valid DNS names (default is "puppet"), so agents can trust the master if they contact it. You should make sure that this static list contains the DNS name or alias you’ll be configuring your agents to contact.

   c. **SSH username**: provide the username to use when connecting to the Puppet master. This field defaults to `root`. This user must either be root or have sudo access.

   d. **SSH password**: if used, provide the password for the SSH username provided. This password will also be used if the user requires a password for sudo access.

   e. **SSH key file path**: if a SSH password is not used, provide the absolute path to the SSH key on the machine you are performing the installation from. Defaults to the root SSH key path.

   f. **SSH key passphrase**: (optional) provide if your SSH key is protected with a passphrase.

7. Provide the following information about the PuppetDB server:

   a. **PuppetDB hostname**: provide the fully qualified domain name of the server you're installing the PuppetDB on.

   b. **SSH username**: provide the username to use when connecting to PuppetDB. This field defaults to `root`. This user must either be root or have sudo access.

   c. **SSH password**: if used, provide the password for the SSH username provided. This password will also be used if the user requires a password for sudo access.

   d. **SSH key file path**: if a SSH password is not used, provide the absolute path to the SSH key on the machine you are performing the installation from. Defaults to the root SSH key path.

   e. **SSH key passphrase**: provide if your SSH key is protected with a passphrase.

8. Provide the following information about the console server:

   a. **Console hostname**: provide the fully qualified domain name of the server you're installing the PE console on.

   b. **SSH username**: provide the username to use when connecting to the PE console. This field defaults to `root`. This user must either be root or have sudo access.

   c. **SSH password**: if used, provide the password for the SSH username provided. This password will also be used if the user requires a password for sudo access.

   d. **SSH key file path**: if a SSH password is not used, provide the absolute path to the SSH key on the machine you are performing the installation from. Defaults to the root SSH key path.

   e. **SSH key passphrase**: provide if your SSH key is protected with a passphrase.

9. Provide the following information about database support (PuppetDB and PostgreSQL):

   a. **Install PostgreSQL on the PuppetDB host for me**: (default) PE will install a PostgreSQL instance for the databases on the same node as PuppetDB. This will use PE-generated default names and usernames for the databases. The passwords can be retrieved from `/etc/puppetlabs/installer/database_info.install` when the installation is complete.

   b. **Use an Existing PostgreSQL instance**: if you already have a PostgreSQL instance you'd like to use, you'll need to provide the following database information. Refer to [External PostgreSQL option and prep notes](./install_system_requirements.html#external-postgresql-option-and-prep-notes) for more instructions about setting up your databases.

   > **Important**: After installing PE, refer to the [SSL for PE and PostgreSQL documentation](./install_ssl_postgresql.html) to enable SSL between PE and your external PostgreSQL instance.

   - the PostgreSQL server DNS name

   - the port number used by the PostgreSQL server (default is 5432)

   - the PuppetDB database name (default is "pe-puppetdb")

   - the PuppetDB database user (default is "pe-puppetdb).

   - the PuppetDB database password

   - the role-based access control database name (default is "pe-rbac")

   - the role-based access control database user (default is "pe-rbac")

   - the role-based access control database password

   - the node classifier database name (default is "pe-classifier")

   - the node classifier database user (default is "pe-classifier")

   - the node classifier database password

   - the activity database name (default is "pe-activity")

   - the activity database user (default is "pe-activity")

   - the activity database password

10. Provide the following information about the PE console administrator user:

   **Console superuser password**: create a password for the console login; the password must be at least eight characters.
   
   **Note**: the user name for the console administrator user is __admin__.

11. Click **Submit**.

12. On the confirm plan page, review the information you provided, and, if it looks correct, click **Continue**.

    If you need to make any changes, click **Go Back** and make whatever changes are required.

13. On the validation page, the installer will verify various configuration elements (e.g., if SSH credentials are correct, if there is enough disk space, and if the OS is the same for the various components). If there aren't any outstanding issues, click **Deploy now**.

    At this point, PE will begin installing your deployment, and you can monitor the installation as it runs by toggling **Log View** and **Summary View** (top-right corner of page). If you notice any errors during the installation, check `/var/log/puppetlabs/installer/install_log.lastrun.<hostname>.log` on the machine from which you are running the installer.
   
    When the installation is complete, the installer script that was running in the terminal will close itself.

12. Click **Start using Puppet Enterprise** to [log into the console](./console_accessing.html) or continue on to [installing agents](./install_agents.html).

## The `pe.conf` file

As part of the installation, PE generates a [`pe.conf` file](./install_text_mode.html#the-peconf-file), which provides the essential details needed to install and configure PE. When the installation completes, you can find this file at `/opt/puppetlabs/puppet/share/installer/conf.d`. The `pe.conf` file can be used to perform a [text-mode installation](./install_text_mode.html).


## Port forwarding to the installer

If you cannot connect directly to port 3000 on the on the machine you're running the installer from, we suggest port forwarding (or "tunneling" to) the installer via SSH.

**From a Linux machine**:

1. On the machine from which you're running the installer, run `ssh -L 3000:localhost:3000 jumphost.exmple.tld`
2. Run the installer script as indicated in the instructions below.
3. When prompted to enter the installer URL, instead navigate to `https://localhost:3000`.

**From a Windows machine**:

1. Open PuTTY, and select **Sessions**.
2. In the **Host Name** field, enter the FQDN of the host you want to run the installer from.
3. Select **Tunnels**.
4. In the **Source Port** field, enter `3000`.
5. In the **Destination** field, enter `localhost:3000`.
6. Select **Local**.
7. Click **Add**.
8. Click **Open**.

When the installer asks you to launch the browser, use `https://localhost:3000`, and continue following the installation instructions.

***

Next: [Installing PE agents](./install_agents.html)

Related link: [PE multimaster installation](./install_multimaster.html)
