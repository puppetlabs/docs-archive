# Start installing PE on \*nix

This guide uses a web-based installer to walk you through a basic installation. This installation type is ideal for trying out PE with up to 10 nodes, and can be used to manage up to 4000 nodes. This basic setup installs the Puppet master, the PE console, and PuppetDB all on one node, a \*nix machine. This guide also reviews prerequisites necessary to a PE installation.

On a single \*nix machine, you'll install:

-   The master, the central hub of activity, where Puppet code is compiled to create agent catalogs, and where SSL certificates are verified and signed.
-   The PE console, the web interface, which features numerous configuration and reporting tools.
-   PuppetDB, which collects data generated throughout your Puppet infrastructure.

Once this installation is complete, you'll move on to installing Puppet Enterprise on the nodes you wish to manage with Puppet, commonly known as agent nodes.

## Web-based installation prerequisites

Review these prerequisites and tips before beginning a web-based installation.

-   If you've previously installed Puppet or Puppet Enterprise, make sure that the machine you're installing on is free of any artifacts left over from the previous installation.

-   Make sure that DNS is properly configured on the machines you're installing on.

    -   All nodes must know their own hostnames, which you can achieve by properly configuring reverse DNS on your local DNS server, or by setting the hostname explicitly. Setting the hostname usually involves the `hostname` command and one or more configuration files, but the exact method varies by platform.

    -   All nodes must be able to reach each other by name, which you can achieve with a local DNS server, or by editing the `/etc/hosts` file on each node to point to the proper IP addresses.

-   You can run the installer from a machine that is part of your deployment or from a machine that is outside your deployment. If you want to run the installer from a machine that is part of your deployment, in a split installation, run the installer from the node assigned the console component.

-   The machine you run the installer from must have the same operating system and architecture as your deployment.

-   The web-based installer does not support sudo configurations with `Defaults targetpw` or `Defaults rootpw`. Make sure your `/etc/sudoers` file does not contain, or comment out, those lines.

-   For Debian users, if you gave the root account a password during installation of Debian, sudo may not have been installed. In this case, you must either install as root, or install sudo on any nodes on which you want to install.


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


## Install using web-based configuration

Web-based installation uses a web server to guide you through installation.

### About this task

Make sure you have reviewed the prerequisites in a previous section before you begin installing.

### Procedure

1.  Unpack the installation tarball:

    ```
    tar -xf <TARBALL_FILENAME>
                      
    ```

2.  From the installer directory, run the installer:

    ```
    sudo ./puppet-enterprise-installer
    ```

3.  When prompted, choose guided installation

    The installer starts a web server and provides an installer URL.

    The default installer URL is `https://<INSTALL_PLATFORM_HOSTNAME>:3000`. If you forwarded ports in step 1, the URL is `https://localhost:3000`.

4.  In a browser, access the installer URL and accept the security request.

    The installer uses a default SSL certificate. You must add a security exception in order to access the installer.

    **Important:** Leave your terminal connection open until the installation is complete or else installation fails.

5.  Follow the prompts to configure your installation.

6.  On the validation page, verify the configuration and, if there aren't any outstanding issues, click **Deploy now**.

    Installation begins. You can monitor the installation as it runs by toggling **Log View** and **Summary View**. If you notice errors, check `/var/log/puppetlabs/installer/install_log.lastrun.<hostname>.log` on the machine from which you're running the installer.

    When the installation completes, the installer script that was running in the terminal closes.

7.  Click **Start using Puppet Enterprise** to log into the console.


### Results

## Log into the PE console

On the validation page, the installer verifies various configuration elements \(for example, if SSH credentials are correct, if there is enough disk space, and if the OS is the same for the various components\).

### About this task

### Procedure

1.  When you click **Start using Puppet Enterprise**, you receive a browser warning about an untrusted certificate. This is because you were the signing authority for the console's certificate, and your Puppet Enterprise deployment is not known to your browser as a valid signing authority. Ignore the warning and accept the certificate.

2.  On the login page for the console, log in with the username `admin` and the password you created when installing the Puppet master.


### Results

Congratulations, you've successfully installed the Puppet master node! Next you'll install PE on your agent nodes, so that you can manage these nodes with Puppet.

