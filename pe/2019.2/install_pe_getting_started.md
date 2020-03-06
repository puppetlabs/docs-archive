# Start installing PE on \*nix

The express install of PE is ideal for trying out PE with up to 10 nodes and can be used to manage up to 4,000 nodes.

Here are some general guidelines for what is required depending on your system capacity to download and install PE.



On a single \*nix machine, you install:

-   The master, the central hub of activity, where Puppet code is compiled to create agent catalogs, and where SSL certificates are verified and signed.
-   The PE console, the graphical web interface, which features numerous configuration and reporting tools.
-   PuppetDB, which collects data generated throughout your Puppet infrastructure.

## Download and verify the installation package

PE is distributed in downloadable packages specific to supported operating system versions and architectures. Installation packages include the full installation tarball and a GPG signature \(.asc\) file used to verify authenticity.

### Before you begin

GnuPG is an open source program that allows you to safely encrypt and sign digital communications. You must have GnuPG installed to sign for the release key. Visit the [GnuPG website](https://gnupg.org/) for more information or to download GnuPG.

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

Express installation uses default settings to install PE, so you don't have to edit a `pe.conf` file before or during installation. At the end of the installation process, you're prompted to provide a console administrator password, which is the only user-required value.

### About this task

Log in as a root user to perform these steps.

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

4.  After installation completes, follow the prompts to specify a password, or run: `puppet infrastructure console_password --password=<MY_PASSWORD>`

5.  Run Puppet twice: `puppet agent -t`.


### What to do next

You must restart the shell before you can use PE client tool subcommands.

## Log into the PE console

The console is a graphical interface that allows you to manage parts your PE infrastructure without relying on the command line.

### About this task

Log in for the first time.

### Procedure

1.  When you navigate to the hostname of the master in your browser, you'll receive a browser warning about an untrusted certificate. This is because you were the signing authority for the console's certificate, and your Puppet Enterprise deployment is not known to your browser as a valid signing authority. Ignore the warning and accept the certificate.

2.  On the login page for the console, log in with the username `admin` and the password you created when installing the Puppet master. Keep track of this login as you will use it later.


### Results

Congratulations, you've successfully installed the Puppet master node! Next, learn how to install an agent on a node using the console.

