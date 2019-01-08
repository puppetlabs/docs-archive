---
layout: default
title: "Installing Puppet Enterprise for a Windows environment"
canonical: "/pe/latest/windows_installing.html"

---
[putty config]: images/windows/putty_config.png
[login as root]: images/windows/login_as_root.png
[disable iptables]: images/windows/disable_iptables.png
[cd to tarball]: images/windows/cd_tarball.png
[get started]: images/windows/get_started.png
[master install]: images/windows/master_install.png
[master install two]: images/windows/master_install_two.png
[confirm plan]: images/windows/confirm_plan.png
[classification page]: images/windows/classification.png
[add class]: images/windows/add_class.png
[puppet run]: images/windows/puppet_agentt.png
[run puppet two]: images/windows/install_puppetagent.png

This guide describes the process for installing a Puppet Enterprise master and agent. A master is the node that controls configuration information. An agent is the node that the master controls, such as a Windows machine. The two communicate via HTTPS with client verification. You can find out more about master and agent architecture in the [Puppet architecture documentation]({{puppet}}/architecture.html).

Puppet masters can only run on *nix machines, but Windows machines can run as Puppet agents. For this reason, this guide shows you how to use a Windows machine to install a Puppet master on a Linux server and then install a Puppet agent on your Windows machine. In total you will:

* Step 1: Prep Your System
* Step 2: Download the Puppet Enterprise Master Package
* Step 3: Install the Puppet Master on Your Linux Machine
* Step 4: Install a Puppet Agent on the Windows Machine

## Before you begin

To perform these steps, you'll need a Windows machine and a Linux server. The following examples use a Linux server running Red Hat Enterprise Linux (RHEL) 6.

Your Linux machine (aka the Puppet master) will need:

* A designated hostname.
* A fully qualified domain name (FQDN).
* 6GB of RAM and 4 cores. See [PE System Requirements](./install_system_requirements.html).
* Accessibility over port 3000.
* The ability to log in as the root user, or as a user who has sudo privileges.

## Step 1: Prep your Windows system

On your Windows machine:

1. If you don’t already have an SSH client installed, install one.
  The following example uses [Putty](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
2. If you don’t already have an SFTP client installed, install one.
  Popular clients include FileZilla and WinSCP.

## Step 2: Download the Puppet Enterprise master package

Before you can manage a machine running the Windows operating system, you must first install the Puppet master on a Linux server, because the Puppet master cannot run on Windows.

After you install the Puppet master, you will install PE on your Windows machine.

* First, on your Windows machine, download the PE Master installer package for **EL (RHEL, CentOS, Scientific Linux, Oracle Linux)** version 6 from the [Puppet Enterprise download page](https://puppetlabs.com/download-puppet-enterprise).

## Step 3: Install the Puppet master on Your Linux machine

1. On your Windows machine, using your SFTP client, copy the PE Master installer package onto the Linux machine you’ve designated to be your Puppet master.
2. On your Windows machine, using your SSH client, ssh into your Linux machine, and enter the hostname or IP address and port, as shown in the following example.
  ![putty config][putty config]
3. When prompted in the terminal, log into your Linux machine as the `root` user, or as a user that has sudo privileges.
  ![login as root][login as root]
4. Your Linux machine should have a designated hostname. To verify the hostname, run `hostname -f`.
5. To access the web-based installer used by PE, make sure port 3000 is accessible.
    >**Tip**: Although the method for doing so depends on both the OS and cloud/virtualization platform you’re using, you can ensure this port is open by disabling `iptables`, as shown in the following example.  
  ![disable iptables][disable iptables]  
6. Extract the PE installer tarball by running, `tar -xvf puppet-enterprise-2015.2.0-*`.
7. Move into the installer directory by running, `cd puppet-enterprise-2015.2.0-el-6-x86_64`.
  ![cd to tarball][cd to tarball]
8. Start the Puppet Enterprise installation script. Run `./puppet-enterprise-installer`.
9. Guided Installation information will be displayed. When prompted, answer `y` to install the necessary packages to perform a guided installation. A message says, "Installing setup packages," and then another message tells you to go to a URL in your browser. For example, "https://master.inf.puppetlabs.demo:3000".
10. Copy the URL provided by the installer, and paste it into your browser. When prompted, click **Let’s get started!**.
  ![get started][get started]
11. Choose a deployment architecture. These setup instructions assume a monolithic installation. For more information, refer to the [installation overview](./install_basic.html#choose-an-installation-method).
  >**Note**: From this point the examples will refer to your Linux Machine as the Puppet master.
12. In the corresponding fields, enter your Puppet master’s FQDN and alias, as below.
  ![master install][master install]
  And then add a console admin password.
  ![second install image][master install two]
  >**Important**: Your Puppet agents will identity the Puppet master via its alias, so you must identify this correctly. The default is “puppet”.
13. Click **Submit**.
14. In the **Confirm the plan** screen, verify that the choices are correct, and click **Continue**.
  ![confirm plan][confirm plan]
15. Puppet Enterprise verifies that your system meets all of the prerequisites. If not, you can use the **Log View** toggle to check the log. When it's finished, click **Deploy now**.
  >**Note**: The installer takes several minutes to complete.
16. Click **Start using Puppet Enterprise**.
17. Log in with the console admin password you provided during installation. The user name is "admin".

Congratulations! You have successfully installed the Puppet Enterprise master!

### See your node in the web UI

When you log into the web UI, you’re taken to the **Overview** page. This page shows a list of all of the machines that have a PE agent installed on them and are communicating with your Puppet master. The Puppet master itself has an agent installed so that Puppet can keep it configured correctly---that’s why you see it here in this list.

After you install a Puppet agent in the next example, it will check in with the Puppet master, and once you give the OK to have that agent communicate with the Puppet master, the machine that that agent is running on will show up in the **Overview** list as well.

## Step 4: Install a Puppet agent on your Windows machine

In the following steps, you'll install a Puppet agent on your Windows machine. However, before you can install the agent, you have to configure the Puppet master so that it has the correct agent installer for your system.

You do this using the PE console.

1. In the right menu bar, click **Nodes**.
2. Click **Classification**.
3. On the **Classification** page, click the **PE_Master** group.
  ![classification page][classification page]
4. Click the **Classes** tab.
5. In the **Add new class** field, type “windows” and select the architecture that matches your Windows machine.
  ![add class][add class]
6. Click **Add class** and then click the **Commit** button.
  At this point you’ve changed the classification of the agent running on the Puppet master, and now want the master “to be in a different state.” In order for puppet to see that you’ve made this change, you can either wait the standard 30 minute interval for the puppet agent to wake up, or you can kick off a puppet run manually on the master. Let’s do that now.

#### Optional: Manually run Puppet

1. Using your SSH client, log into the Puppet master.
2. Kick off a Puppet run with `puppet agent -t`.
  ![puppet run][puppet run]
  This Puppet run will download the Puppet agent for Windows onto your Puppet master.
3. **On your Windows machine**, point your browser at the following URL: `https://<YOUR PUPPET MASTER HOSTNAME>:8140/packages/current/windows-x86_64/puppet-agent-x64.msi`.
  This will download the .msi package you need to install the Puppet agent on your Windows machine.
4. On your Windows machine, install the Puppet agent msi, as shown in the following example.
  ![run puppet two][run puppet two]
5. When prompted about file security, click **Run**.
6. When the setup wizard starts, click **Next**.
7. Accept the Licence Agreement, and click **Next**.
8. Enter the FQDN of your Puppet master, and click **Next**.
9. When it's ready to install, click **Install**.
10. When the setup wizard completes, click **Finish**.
11. Run PowerShell as an administrator.
12. On the PowerShell command line, kick off a Puppet run using `puppet agent -t`.

> **Note:** By end of year 2016, running 32-bit Puppet agent on a 64-bit Windows system will be deprecated. Update your Puppet installation to the 64-bit platform by December 31, 2016.

## Imperative: Sign your node certificate
Whether you run Puppet manually, or wait for it to run as part of the regular 30 minute run, on first installing an agent, a certificate signing request (CSR) is created. You must sign the certificate for that agent before the install is complete and the node can be managed.
1. In your brower, return to the PE console, and click **Nodes**.
2. On the **Nodes** page, click the **Unsigned certificates** tab.
3. In the list of node names, locate your Windows machine and click **Accept**.


### What's next

Now that you've finished installing PE and a Windows agent, you're ready to see what you can do with Puppet. Go to [Managing Windows configurations](./windows_config_mgmnt.html) to learn how to automate managing services, scheduling tasks, managing users and groups, and more.
See [Installing and using Windows modules](./windows_modules.html) to take advantage of pre-existing modules---essentially, Puppet code solutions---that jump-start additional automation tasks.
