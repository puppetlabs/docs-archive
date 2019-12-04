---
author: Michelle Fredette <michelle.fredette@puppet.com\>
---

# Getting started: Installing PE for Windows

This getting started guide uses a web-based installer to walk you through a basic monolithic Puppet Enterprise installation, ideal for trying out PE with up to 10 nodes, or for managing up to 4000 nodes.

**Important:** The Puppet master components can currently be installed on a Linux machine only. Puppet agent components can be installed on Windows machines and you can manage those machines with your Linux Puppet master. The task of installing Puppet Enterprise is the same as in the \*nix getting started guide.

If you plan to access the master \*nix machine remotely from a Windows machine, then you should follow the steps to install a master and agent on the Windows installing page. In that case, complete the master and agent install and then return to the module installation page to carry on with this guide.

On a single \*nix machine, you'll install:

-   The Puppet master, the central hub of activity, where Puppet code is compiled to create agent catalogs, and where SSL certificates are verified and signed.
-   The console, PE's web interface, which features numerous configuration and reporting tools.
-   PuppetDB, which collects data generated throughout your Puppet infrastructure.

Once this installation is complete, you'll move on to installing Puppet Enterprise on the nodes you wish to manage with Puppet, commonly known as agent nodes.

## Step 1: Review installation prerequisites

Before getting started, review this checklist to make sure you're ready to install PE.

### Procedure

1.  Be aware that you'll need to work as the `root` user on the command line throughout the installation process.

2.  Make sure you meet the hardware requirements.

    Minimum hardware for 10 or fewer nodes \(you can download and install Puppet Enterprise on up to 10 nodes at no charge\):

    |Cores|RAM|/opt/|EC2|
    |-----|---|-----|---|
    |2|6 GB|20 GB|m3.xlarge instance|

    If you wish to manage more than 10 nodes, consult the hardware recommendations.

3.  Make sure that all of the nodes you plan to use PE with know their own hostnames. This can be done by properly configuring reverse DNS on your local DNS server, or by setting the hostname explicitly.

4.  Make sure all nodes are able to reach each other by name. This can be done with a local DNS server, or by editing the `/etc/hosts` file on each node to point to the proper IP addresses.

5.  Ensure port 3000 is reachable, because the web-based installer uses this port.

6.  Know the fully qualified domain name of the server you're installing PE on. \(For example, `master.example.com`.\)


**Related topics**  


[Hardware requirements](hardware_requirements.md#)

## Step 2: Download and unpack the tarball

All the files you need to install Puppet Enterprise are bundled together in a single compressed file called a tarball.

### About this task

### Procedure

1.  Visit our [downloads page](https://puppet.com/download-puppet-enterprise?_ga=2.195400108.1038057021.1505707881-652786455.1484937422)​ and download the full tarball that matches your operating system's version and architecture.

    |To install on...|Select a filename ending with...|
    |----------------|--------------------------------|
    |RHEL, CentOS, Scientific Linux, or Oracle Linux|-el-<version and arch\>.tar.gz|
    |Ubuntu LTS|-ubuntu-<version and arch\>.tar.gz|
    |SLES|-sles-<version and arch\>.tar.gz|

2.  Puppet uses a digital signature to authenticate its files. You can verify the integrity of the tarball by following the instructions to verify the installer.

3.  Run the following command to unpack the tarball:

    `tar -xf <TARBALL>` ****

    **Note:** You need about 1 GB of space in `/tmp` to untar the installer.

4.  Next, set up the installer. From the PE installer directory, run:

    `sudo ./puppet-enterprise-installer`

5.  When prompted, choose the “Guided” installation option.

6.  The PE installer will now start a web server and provide a web address: `https://<install platform hostname>:3000`. Copy this address into your browser \(make sure to use `https`\).

    Leave your terminal connection open until installation is complete, or the installation will fail.

7.  When prompted, accept the security request in your browser. The web-based installation uses a default SSL certificate, so you must add a security exception in order to access the web-based installer. This is safe to do.


### Results

**Related topics**  


[Accepting the console's certificate](console_accessing.md#)

## Step 3: Personalize your installation

You've successfully launched the PE web-based installer! On the installer start page, click Let's get started!. The installer first collects some key configuration information, which is used to make sure your installation is set up correctly.

### Procedure

1.  Choose **Install on this server**, which will install the Puppet master component on the server you're running the installer from.

2.  Provide the following information about the Puppet master server:

    -   Puppet master FQDN: The fully qualified domain name of the server you're installing PE on.

    -   DNS aliases: A comma-separated list of aliases that your agent nodes \(the nodes you wish to manage with PE\) can use to reach the Puppet master.

3.  When prompted about database support, choose the default option, **Install PostgreSQL for me**.

4.  Create a password for the console administrator. The password must be at least eight characters. The user name for the console administrator is always "admin."

5.  Finally, click **Submit**, and then review the information you provided. If you need to make any changes, click **Go back** and make whatever changes are required. Otherwise, click **Continue**.


## Step 4: Complete installation and log into the PE console

On the validation page, the installer verifies various configuration elements \(for example, if SSH credentials are correct, if there is enough disk space, and if the OS is the same for the various components\).

### About this task

### Procedure

1.  If there are no outstanding issues, click **Deploy Now**. The installer installs and configures PE. It may also need to install additional packages from your OS’s repository. This process may take up to 15 minutes.

    When installation is complete, the installer script that was running in the terminal closes itself.

2.  Click the **Start using Puppet Enterprise** button that appears at the end of the web-based installer.

3.  You will receive a browser warning about an untrusted certificate. This is because *you* were the signing authority for the console's certificate, and your Puppet Enterprise deployment is not known to your browser as a valid signing authority. Ignore the warning and accept the certificate.

4.  On the login page for the console, log in with the username admin and the password you created when installing the Puppet master.


### Results

Congratulations, you've successfully installed the Puppet master node! Next you'll install PE on your agent nodes, so that you can manage these nodes with Puppet.

