---
layout: default
title: "Web-based installation: monolithic"
canonical: "/pe/latest/install_pe_mono.html"
---

In a monolithic installation all PE components are installed on one node. This installation type is easy to install, upgrade, and troubleshoot, and you can expand this installation type up to 20,000 managed nodes by adding compile masters to it as you scale. A monolithic installation is the recommended install type for most users. 

The web-based installer provides an interactive GUI to guide you through the installation process.

## Web-based installation prerequisites and notes

Review the following general notes and SSH prerequisites before performing a web-based installation of PE.

### General prep notes

- If you've previously installed Puppet or PE, make sure that the machine you're installing PE on is totally free of any artifacts left over from the previous installation.

- Make sure that DNS is properly configured on the machines you're installing PE on. All nodes must **know their own hostnames.** This can be done by properly configuring reverse DNS on your local DNS server, or by setting the hostname explicitly. Setting the hostname usually involves the `hostname` command and one or more configuration files, while the exact method varies by platform. In addition, all nodes must be able to **reach each other by name.** This can be done with a local DNS server, or by editing the `/etc/hosts` file on each node to point to the proper IP addresses.

- You can run the installer from a machine that is part of your PE deployment or from a machine that is outside your deployment. If you want to run the installer from a machine that is part of your deployment, we recommend you run it from the same node assigned the console component (in a split install).

- The machine you run the installer from must have the same OS/architecture as your PE deployment.

- Please ensure that port 3000 is reachable, as the web-based installer uses this port. You can close this port when the installation completes. If you can't connect directly to port 3000, refer to [Port forwarding to the web-based installer](#port-forwarding-to-the-web-based-installer) for a potential workaround.

- The web-based installer does not support sudo configurations with `Defaults targetpw` or `Defaults rootpw`. Make sure your `/etc/sudoers` file does not contain, or else comment out, those lines.

- **For Debian Users**: If you gave the root account a password during the installation of Debian, sudo may not have been installed. In this case, you will need to either install PE as root, or install sudo on any node(s) on which you want to install PE.

- **A Note about Passwords**: In some cases, during the installation process, you'll be asked to supply passwords. The `'` (single quote) is forbidden in all passwords.

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
     * **Prerequisite**: Remote root SSH login must enabled on each node, including the node from which you're running the installer. And the public root ssh key must be added to `authorized_keys` on each node on which you're installing a PE component.

   - **Are you installing using a non-root user with an SSH key?** The installer will ask you to provide the username, private key path, and key passphrase (as needed) for each node on which you're installing a PE component.
     * **Prerequisite**: The non-root user SSH key must be added to `authorized_keys` on each node on which you're installing a PE component. And the non-root user must be granted sudo access on each box.


## Install PE: monolithic installation

When you perform a monolithic installation of PE, the master, console, and PuppetDB components are all installed on the same machine. 

Before you begin:

- Review the [web-based installation prerequisites and notes](#web-based-installation-prerequisites-and-notes)
- [Download and verify the appropriate PE tarball](./install_basic.html#downloading-puppet-enterprise)

1. Unpack the PE installation tarball. Run `tar -xf <tarball>`. (Note that you need about 1 GB of space in `/tmp` to untar the installer.) 
2. From the installer directory, run the installer with `sudo ./puppet-enterprise-installer`. 
3. When prompted, choose the “Guided” installation option. 

   At this point, the PE installer starts a web server and provides a web address: `https://<install platform hostname>:3000`. Ensure that port 3000 is reachable. You can close port 3000 when the installation completes.
   
   >**Warning**: Leave your terminal connection open until the installation is complete; otherwise, the installation will fail.
 
4. Paste the provided web address into your browser, and when prompted, accept the security request.

   >**Note:** The web-based installation uses a default SSL certificate; you’ll have to add a security exception in order to access the installer. This is safe to do.

5. On the start page, click **Let's get started**.
6. Choose whether to **Install on this server** or **Install on another server**.
7. Provide values for the following settings.

   > **Note:** If you selected **Install on this server**, you will only be prompted for the Puppet master FDQN and DNS altnames.
   
   <table>
     <tr>
       <th>Setting</th>
       <th>Value</th>
     </tr>
     <tr>
       <td nowrap width="10">Puppet master FQDN</td>
       <td>Provide the fully qualified domain name of the server you're installing PE on. This becomes the name of the Puppet master certificate. This FQDN must be resolvable from the machine on which you're running the installer. <br>To ensure you're using the proper FQDN for the monolithic Puppet master, run <code>sudo /opt/puppetlabs/bin/puppet config print certname</code> when the installation completes. If the installation fails because the FQDN value is incorrect, run the <code>config print certname</code> command and re-run the installer with the correct value.</td>
     </tr>
     <tr>
       <td nowrap width="10">DNS altnames</td>
       <td>Provide a comma-separated list of static, valid DNS altnames so agents can trust the master if they contact it. You should make sure that this static list contains the DNS name or alias you’ll be configuring your agents to contact. The default install includes "puppet".</td>
     </tr>
     <tr>
       <td nowrap width="10">SSH username</td>
       <td>Provide the username to use when connecting to the Puppet master. This field defaults to <code>root</code>. This user must either be root or have sudo access.</td>
     </tr>
     <tr>
       <td nowrap width="10">SSH password</td>
       <td>If used, provide the password for the SSH username provided. This password will also be used if the user requires a password for sudo access.</td>
     </tr>
     <tr>
       <td nowrap width="10">SSH key file path</td>
       <td>If SSH password is not used, provide the absolute path to the SSH key on the machine you are performing the installation from. Defaults to the root SSH key path.</td>
     </tr>
     <tr>
       <td nowrap width="10">SSH key passphrase</td>
       <td>Provide if your SSH key is protected with a passphrase.</td>
     </tr>
   </table>     

8. Provide the following information about database support (PuppetDB and PostgreSQL):

   a. **Install PostgreSQL on the PuppetDB host for me**: (Default) PE will install a PostgreSQL instance for the databases. This will use PE-generated default names and usernames for the databases. The passwords can be retrieved from `/etc/puppetlabs/installer/database_info.install` when the installation is complete.

   b. **Use an Existing PostgreSQL instance**: If you already have a PostgreSQL instance you'd like to use, you'll need to provide the following database information. Refer to [External PostgreSQL option and prep notes](./sys_req_extsql.html) for additional instructions about setting up your databases.

   - The PostgreSQL server DNS name

   - The port number used by the PostgreSQL server (default is 5432)

   - The PuppetDB database name (default is "pe-puppetdb")

   - The PuppetDB database user (default is "pe-puppetdb).

   - The PuppetDB database password

   - The role-based access control database name (default is "pe-rbac")

   - The role-based access control database user (default is "pe-rbac")

   - The role-based access control database password

   - The node classifier database name (default is "pe-classifier")

   - The node classifier database user (default is "pe-classifier")

   - The node classifier database password

   - The activity database name (default is "pe-activity")

   - The activity database user (default is "pe-activity")

   - The activity database password
   
   - The orchestrator database name (default is "pe-orchestrator")
   
   - The orchestrator database user (default is "pe-orchestrator")
   
   - The orchestrator database password
   
     > **Important**: After installing PE, refer to the [SSL for PE and PostgreSQL documentation](./install_ssl_postgresql.html) to enable SSL between PE and your external PostgreSQL instance.

7. Provide the following information about the PE console admin user, and then click **Submit**.

   **Console superuser password**: Create a password for the console login. The password must be at least eight characters.

   >**Note**: The user name for the console admin user is __admin__.

8. On the confirm plan page, review the information you provided, and, if it looks correct, click **Continue**.

    If you need to make any changes, click **Go Back** and make whatever changes you need.

9. On the validation page, the installer verifies various configuration elements. If there aren't any outstanding issues, click **Deploy now**.

    At this point, the PE installation starts. You can monitor the installation as it runs by toggling **Log View** and **Summary View**. If you notice any errors during the installation, check `/var/log/puppetlabs/installer/install_log.lastrun.<hostname>.log` on the machine from which you are running the installer.
   
    When the installation completes, the installer script that was running in the terminal will close. 
    
10. Click **Start using Puppet Enterprise** to [log into the console](./console_accessing.html). 

>**Tip:** If you purchase Puppet Enterprise, you are sent a `license.key` file that lists how many nodes you can deploy. Refer to [Install the PE license key](install-the-pe-license-key) for instructions on installing that key. 

## Port forwarding to the web-based installer

The web-based installer requires access to port 3000 on the machine you're running the installer from. If you cannot connect directly to port 3000, we suggest port forwarding (or "tunneling" to) the installer via SSH.

The method for enabling port forwarding depends on your platform.

### Port forwarding from a *nix machine

1. On the machine from which you're running the installer, run `ssh -L 3000:localhost:3000 jumphost.exmple.tld`.
      
2. Run the installer script as indicated in the [web-based installation instructions](#install-pe-monolithic-installation).
      
3. When prompted to enter the installer URL, instead navigate to `https://localhost:3000`.


### Port forwarding from a Windows machine 
      
1. Open PuTTY, and select **Sessions**.
       
2. In the **Host Name** field, enter the FQDN of the host you want to run the installer from.
       
3. Select **Tunnels**.
       
4. In the **Source Port** field, enter `3000`.
       
5. In the **Destination** field, enter `localhost:3000`.
       
6. Select **Local**.
       
7. Click **Add**, and then click **Open**.

8. When the installer asks you to launch the browser, use `https://localhost:3000`, and continue following the installation instructions. 

## Add Puppet Enterprise binaries to `PATH`

PE [installs a number of binaries](./install_what_and_where.html#executable-binaries-and-symlinks) for interacting with its tools and services. You should include these binaries in your default path.

1. To include these binaries in your default `$PATH`, manually add them to your profile or run:
   
   ~~~
   PATH=/opt/puppetlabs/puppet/bin:/opt/puppetlabs/server/bin:$PATH;export PATH
   ~~~
      
## Disable Puppet Enterprise symlinks

PE [installs a number symlinks](./install_what_and_where.html#where-is-it-installed) for interacting with its tools and services. If needed, you can disable symlinks by placing a setting in your your [default Hiera file](./config_intro.html#configure-settings-with-hiera).

1. In your default Hiera file, add the following setting:

   ~~~
   puppet_enterprise::manage_symlinks: false
   ~~~

************
**Related links**

- [Installing the PE license key](./install_license_key.html)
