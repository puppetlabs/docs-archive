# Start installing PE on \*nix

This guide walks you through an express installation of PE. This installation type is ideal for trying out PE with up to 10 nodes, and can be used to manage up to 4000 nodes.

On a single \*nix machine, you install:

-   The master, the central hub of activity, where Puppet code is compiled to create agent catalogs, and where SSL certificates are verified and signed.
-   The PE console, the web interface, which features numerous configuration and reporting tools.
-   PuppetDB, which collects data generated throughout your Puppet infrastructure.

When this installation is complete, move on to installing Puppet Enterprise on the nodes you wish to manage with Puppet, commonly known as agent nodes.

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

## Log into the PE console

On the validation page, the installer verifies various configuration elements \(for example, if SSH credentials are correct, if there is enough disk space, and if the operating system is the same for the various components\).

### About this task

### Procedure

1.  When you click **Start using Puppet Enterprise**, you receive a browser warning about an untrusted certificate. This is because you were the signing authority for the console's certificate, and your Puppet Enterprise deployment is not known to your browser as a valid signing authority. Ignore the warning and accept the certificate.

2.  On the login page for the console, log in with the username `admin` and the password you created when installing the Puppet master.


### Results

Congratulations, you've successfully installed the Puppet master node! Next install PE on your agent nodes, so that you can manage these nodes with Puppet.

