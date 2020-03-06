---
author: Michelle Fredette <michelle.fredette@puppet.com\>
---

# Start installing PE in a Windows environment

The express install of PE is ideal for trying out PE with up to 10 nodes and can be used to manage up to 4,000 nodes.

**Important:** The Puppet master can run only on \*nix machines, but Windows machines can run as Puppet agents and you can manage those nodes with your \*nix Puppet master**.** This getting started guide assumes you want to access the master \*nix machine remotely from a Windows machine. If you plan to install directly onto a \*nix node, follow the [installing PE instructions in the \*nix getting started guide](https://puppet.com/docs/pe/2017.3/quick_start_guides/install_pe_getting_started.html).

On a single \*nix machine, you install:

-   The Puppet master, the central hub of activity, where Puppet code is compiled to create agent catalogs, and where SSL certificates are verified and signed.
-   The console, PE's web interface, which features numerous configuration and reporting tools.
-   PuppetDB, which collects data generated throughout your Puppet infrastructure.

## Step 1: Review installation prerequisites on your Linux server

Before getting started, review this checklist to make sure you're ready to install PE.

### About this task

**Note:** The examples in this guide use a Linux server running Red Hat Enterprise Linux \(RHEL\) 6.

### Procedure

1.  Be aware that you must work as the `root` user on the command line throughout the installation process.

2.  Make sure you meet the [hardware recommendations](https://puppet.com/docs/pe/2017.3/installing/hardware_requirements.html#hardware-requirements) for 10 or fewer nodes.

    You can download and install Puppet Enterprise on up to 10 nodes at no charge.

3.  Make sure that DNS is properly configured on the server you're installing on.

    -   All nodes must know their own hostnames, which you can achieve by properly configuring reverse DNS on your local DNS server, or by setting the hostname explicitly. Setting the hostname usually involves the `hostname` command and one or more configuration files, but the exact method varies by platform.

    -   All nodes must be able to reach each other by name, which you can achieve with a local DNS server.

4.  Know the fully qualified domain name \(FQDN\) of the server you're installing PE on, for example, `master.example.com`.


**Related information**  


[Hardware requirements](hardware_requirements.md#)

## Step 2: Prepare your Windows System

Some extra software is necessary so that you can communicate and work between your Windows and Linux machines.

### About this task

### Procedure

1.  On your Windows machine set up an SSH Client. The examples below use the Putty SSH client.


## Step 3: Install the master on your Linux machine

These steps show you how to use a Windows machine to install a Puppet master on a Linux server.

### Procedure

1.  Open the SSH client, and enter the hostname or IP address and port of the Linux machine that you want to use as your master. You can then open an SSH session to the master.

    ![Screenshot of Pu TTY configuration window with an example host and port set for an SSH connection.](Putty.png)

2.  When prompted in the terminal, log into the Linux node as the `root` user.

3.  Download the PE installer to the server that you intend to use as your master by copying the appropriate download URL from the [downloads page](https://puppet.com/download-puppet-enterprise?_ga=2.195400108.1038057021.1505707881-652786455.1484937422) and running:

    ```
    wget --content-disposition '<DOWNLOAD_URL>'
    ```

4.  Run the following command to unpack the tarball:

    ```
       tar -xf <TARBALL> 
    ```

    **Note:** You need about 1 GB of space.

5.  Change directories to the installer directory, and run the installer:

    ```
    sudo ./puppet-enterprise-installer
    ```

6.  When prompted, select express install mode.

7.  After installation completes, follow the prompts to specify a password, or run:

    ```
    puppet infrastructure console_password --password=<MY_PASSWORD>
    ```

8.  Run Puppet twice: `puppet agent -t`.

    You must restart the shell before you can use PE client tool subcommands.


### Results

**Note:** From this point the examples refer to your Linux server as the master.

**Related information**  


[Accepting the console's certificate](console_accessing.md#)

## Log into the PE console

The console is a graphical interface that allows you to manage parts your PE infrastructure without relying on the command line.

### About this task

Log in for the first time.

### Procedure

1.  When you navigate to the hostname of the master in your browser, you'll receive a browser warning about an untrusted certificate. This is because you were the signing authority for the console's certificate, and your Puppet Enterprise deployment is not known to your browser as a valid signing authority. Ignore the warning and accept the certificate.

2.  On the login page for the console, log in with the username `admin` and the password you created when installing the Puppet master. Keep track of this login as you will use it later.


### Results

Congratulations, you've successfully installed the Puppet master node! Next, learn how to install an agent on a node using the console.

