---
layout: default
title: "Install Puppet Enterprise quick start guide"
canonical: "/pe/latest/quick_start_install_mono.html"
---

**Welcome to Puppet Enterprise!** This quick start guide uses a web-based installer to walk you through a basic Puppet Enterprise installation. This installation type is ideal for trying out PE with up to 10 nodes, and can be used to manage up to 4000 nodes.

On a single *nix machine, you'll install:

* **The Puppet master**, the central hub of activity, where Puppet code is compiled to create agent catalogs, and where SSL certificates are verified and signed.
* **The PE console**, PE's web interface, which features numerous configuration and reporting tools.  
* **PuppetDB**, which collects data generated throughout your Puppet infrastructure.  

Once this installation is complete, you'll move on to installing Puppet Enterprise on the nodes you wish to manage with Puppet, commonly known as agent nodes.   

>#### FAQs
>
> * [Where can I learn more about Puppet Enterprise's architecture?](./pe_architecture_overview.html)
> * [What if I want to manage more than 4000 nodes?](./sys_req_hw.html#choosing-a-pe-architecture)
> * [Can I install PE on a Windows machine?](./windows_installing.html)

## Step 1: Review prerequisites

Before getting started, review this checklist to make sure you're ready to install PE:

1. Be aware that you'll need to work as the `root` user on the command line throughout the installation process.

1. Make sure your hardware needs are met.

   Minimum hardware for 10 or fewer nodes (you can download and install Puppet Enterprise on up to 10 nodes at no charge): 

   Cores | RAM       | /opt/   | EC2
   ------|------ |-----------|---------|------
   2  | 6 GB   | 20 GB  | m3.xlarge instance

   If you wish to manage more than 10 nodes, consult the [hardware recommendations](./sys_req_hw.html).

1. Make sure that all of the nodes you plan to use PE with **know their own hostnames.** This can be done by properly configuring reverse DNS on your local DNS server, or by setting the hostname explicitly. 

1. Make sure all nodes are able to **reach each other by name.** This can be done with a local DNS server, or by editing the `/etc/hosts` file on each node to point to the proper IP addresses. 

1. Ensure port 3000 is reachable, because the web-based installer uses this port.

1. Know the fully qualified domain name of the server you're installing PE on. (For example, `master.example.com`.)

## Step 2: Download the tarball

All the files you need to install Puppet Enterprise are bundled together in a single compressed file called a tarball. Visit [our downloads page](https://puppet.com/download-puppet-enterprise?_ga=1.37644064.1242761972.1463513440) and download the full tarball that matches your operating system's version and architecture.           

|      To install on...        |              Select a filename ending with...                     
|------------------------------|----------------------------------------------------------
| RHEL, CentOS, Scientific Linux, or Oracle Linux | `-el-<version and arch>.tar.gz`  
| Ubuntu LTS                                      | `-ubuntu-<version and arch>.tar.gz` 
| SLES                                            | `-sles-<version and arch>.tar.gz` 

Puppet uses a digital signature to authenticate its files. You can verify the integrity of the tarball by following the [Verifying the installer](./install_basic.html#verifying-the-installer) instructions. 

## Step 3: Unpack the tarball and set up the installer

1. Run the following command to unpack the tarball:

   ```
   tar -xf <TARBALL> 
   ``` 

1. Next, set up the installer. From the PE installer directory, run: 

   ```
   sudo ./puppet-enterprise-installer
   ```

1. When prompted, choose the “Guided” installation option. 

1. The PE installer will now start a web server and provide a web address: `https://<install platform hostname>:3000`. Copy this address into your browser (make sure to use `https`).

    **Leave your terminal connection open** until installation is complete, or the installation will fail.

1. When prompted, accept the security request in your browser. The web-based installation uses a default SSL certificate, so you must add a security exception in order to access the web-based installer. **This is safe to do.** 

>#### FAQs
>
> * [How do I accept a security request in my browser?](./console_accessing.html#accepting-the-consoles-certificate)

## Step 4: Personalize your installation

You've successfully launched PE's web-based installer! On the installer start page, click **Let's get started!**. The installer first collects some key configuration information, which is used to make sure your installation is set up correctly. 

To configure your setup:

1. Choose **Monolithic** as your deployment type. 

1. Choose to install the Puppet master component on the server you're running the installer from.

1. Provide the following information about the Puppet master server:

   1. **Puppet master FQDN**: The fully qualified domain name of the server you're installing PE on.
   
   2. **DNS aliases**: A comma-separated list of aliases that your agent nodes (the nodes you wish to manage with PE) can use to reach the Puppet master. 

1. When prompted about database support, choose the default option, **Install PostgreSQL for me**.

1. **Create a password** for the console administrator. The password must be at least eight characters. The user name for the console administrator is always __admin__.

1. Finally, click **Submit**, and then review the information you provided. If you need to make any changes, click **Go back** and make whatever changes are required. Otherwise, click **Continue**.

## Step 5: Sit back, relax, it's time to install 

On the validation page, the installer verifies various configuration elements (for example, if SSH credentials are correct, if there is enough disk space, and if the OS is the same for the various components). 

If there are no outstanding issues, click **Deploy now**.

The installer then installs and configures Puppet Enterprise. It may also need to install additional packages from your OS's repository. **This process may take up to 15 minutes.** 

When the installation is complete, the installer script that was running in the terminal closes itself. 

## Step 6: Log into the PE console

1. Click the **Start using Puppet Enterprise** button that appears at the end of the web-based installer. 

2. You will receive a browser warning about an untrusted certificate. This is because _you_ were the signing authority for the console's certificate, and your Puppet Enterprise deployment is not known to your browser as a valid signing authority. **Ignore the warning and accept the certificate**. 

3. On the login page for the console, log in with the username **admin** and the password you created when installing the Puppet master.

> Congratulations, you've successfully installed the Puppet master node! Next you'll install PE on your **agent nodes**, so that you can manage these nodes with Puppet. [Click here when you're ready to move on](./quick_start_install_agents_nix.html).


