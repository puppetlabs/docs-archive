---
layout: default
title: "Installing Windows Agents"
canonical: "/pe/latest/install_windows.html"
---


[downloadpe]: http://info.puppetlabs.com/download-pe.html
[puppet.conf]: {{puppet}}/config_file_main.html
[environment]: {{puppet}}/environments.html
[startmenu]: ./images/windows/start_menu.png
[server]: ./images/windows/wizard_server.png
[basic]: ./windows_basic_tasks.html

> ![windows logo](./images/windows-logo-small.jpg) This section refers to Windows functionality. To install PE on \*nix nodes, see [Installing Puppet Enterprise](./install_basic.html).

For supported versions of Windows, see the [supported operating system documentation](./sys_req_os.html).

Windows nodes in Puppet Enterprise:

* Can fetch configurations from a Puppet master and apply manifests locally.
* Can respond to orchestration commands.
* Cannot serve as a Puppet master, console, or database support server.

>**Note:** Puppet *must* be run with elevated privileges (Select *Run as administrator* when opening Command Prompt).

Windows agents can be installed with PE package management or with the Windows .msi package.

## Installing with PE package management

To install a Windows agent with PE package management, you first add the appropriate class that contains the Windows agent packages to `pe_repo`, and then classify the PE Master node group with that class. You then run the install script from the agent to retrieve the necessary packages for installation.

>**Important:** This task requires that you must use PowerShell 2.0 or later.

>**Note:** In the following example, the `<PUPPET MASTER FQDN>` portion of the installer script refers to the fully qualified domain name of the Puppet master. Additionally, this FQDN must be fully resolvable by the Windows machine on which you're installing the agent.

1. In the console, click **Nodes** > **Classification**, and in the **PE Infrastructure** group, select the **PE Masters** group.

2. On the **Classes** tab in the **Class name** field, enter `pe_repo` and select the appropriate repo class from the list of classes.

   - For **64-bit (x86\_64) Windows agents**, select `pe_repo::platform::windows_x86_64`.

   - For **32-bit (i386) Windows agents**, select `pe_repo::platform::windows_i386`.

3. Click **Add class**, and commit changes.

4. On the Puppet master, run Puppet to configure the newly assigned class.

   The new repo is created on the Puppet master in `/opt/puppetlabs/server/data/packages/public/<PE VERSION>/<PLATFORM>/`.

5. On your Windows agent, open an administrative PowerShell window, and run the following command:

   ~~~
   [Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}; $webClient = New-Object System.Net.WebClient; $webClient.DownloadFile('https://<PUPPET MASTER FQDN>:8140/packages/current/install.ps1', 'install.ps1'); .\install.ps1
   ~~~

After running the installer, you should see the following output, which indicates the agent was successfully installed.

~~~Puppet
Notice: /Service[puppet]/ensure: ensure changed 'stopped' to 'running'
service { 'puppet':
  ensure => 'running',
  enable => 'true',
}
~~~

### Passing configuration parameters to the install script

You can pass parameters to the end of the install script to specify configuration settings that are added to `puppet.conf` and to specify settings for inclusion in `custom_attributes` and `extension_requests` sections of `csr_attributes.yaml`. For example:

~~~
[Net.ServicePointManager]::ServerCertificateValidationCallback = {$true}; $webClient = New-Object System.Net.WebClient; $webClient.DownloadFile('https://<PUPPET MASTER FQDN>:8140/packages/current/install.ps1', 'install.ps1'); .\install.ps1 agent:certname=<certnameOtherThanFQDN> custom_attributes:challengePassword=<passwordForAutosignerScript> extension_requests:pp_role=<puppetNodeRole>
~~~

You can pass as many parameters as you need to. Follow the `section:key=value` pattern and leave one space between parameters.

Vist the [Configuration Reference]({{puppet}}/configuration.html) for a complete list of values for `puppet.conf`. Visit the [CSR attributes and certificate extensions]({{puppet}}/ssl_attributes_extensions.html) page for more information on settings for `csr_attributes.yaml`.

## Installing with the .msi package

The PE Windows installer is a standard Windows .msi package and runs as a graphical wizard. The .msi installer package is useful if you need to configure the Puppet agent service's account user, account password, or account domain at installation time.

The installer must be run with elevated privileges. Installing Puppet **does not** require a system reboot.

1. [Download][downloadpe] and run the installer.

2. When prompted by the install dialog, provide the hostname of your Puppet master server.

   ![Puppet master hostname selection][server]

3. Load a list of currently pending node requests by clicking **Nodes** > **Unsigned certificates**.

4. Click *Accept All* to approve the requests and add the node.

> The Puppet agent can now retrieve configurations from the master the next time Puppet runs.

## Installing without internet access

If your infrastructure doesn't have internet access, you must [download][downloadpe] the agent package from an internet-connected system, transfer the package to the expected location on the master, and then follow the instructions for [installing with the .msi package](#installing-with-the-msi-package).

On the master, save the Windows agent installer .msi package to the appropriate location for your system:

* 32-bit systems -- `/opt/puppetlabs/server/data/packages/public/<PE_VERSION>/windows-i386-<AGENT_VERSION>/`
* 64-bit systems -- `/opt/puppetlabs/server/data/packages/public/<PE_VERSION>/windows-x86_64-<AGENT_VERSION>/`

## Installing using a certificate

If you need to perform a secure installation on Windows nodes, you can manually transfer the master CA certificate to target nodes, and run a specialized installation script against that cert.

1. Transfer the installation script and the CA certificate from your master to the node you're installing.

   | File                                | Location on master                                | Location on target node                           |
|-------------------------------------|------------------------------------------------|---------------------------------------------------|
| Installation script (`install.ps1`) | `/opt/puppetlabs/server/data/packages/public/` | Any accessible local directory.                   |
| CA certificate (`ca.pem`)           | `/etc/puppetlabs/puppet/ssl/certs/`            | `C:\ProgramData\PuppetLabs\puppet\etc\ssl\certs\` |

2. Run the installation script, using the `-UsePuppetCA` flag: `.\install.ps1 -UsePuppetCA`


## After installation

* Puppet agent is running as a Windows service, and by default will fetch and apply configurations every 30 minutes. You can now assign classes to the node as normal; see [Getting started with classification](./console_classes_groups_getting_started.html) for more details. After the first Puppet run, the MCollective service will also be running and the node can now be controlled with MCollective. The Puppet agent service and the MCollective service can be started and stopped independently using either the service control manager UI or the command line `sc.exe` utility; see [Running Puppet on Windows]({{puppet}}/services_commands_windows.html) for more details.

* The Start Menu will contain a Puppet folder, with shortcuts for running Puppet agent manually, running Facter, and opening a command prompt for use with the Puppet tools. See [Basic tasks and concepts in Windows][basic] to learn about these tools.

    ![Start Menu icons][startmenu]

* Puppet is automatically added to the machine's PATH environment variable. This means you can open any command line and run `puppet`, `facter` and the few other batch files that are in the `bin` directory of the [Puppet installation](./install_windows.html#program-directory). This will also add necessary items for the Puppet environment to the shell, but only for the duration of execution of each of the particular commands.


## Unattended installation

To install Puppet unattended via the command line, run:

    msiexec /qn /norestart /i puppet.msi

You can also specify `/l*v install.txt` to log the progress of the installation to a file.

### MSI properties

These options are only available when installing Puppet from the command line.

MSI property                                                   | Puppet setting    | Default value
---------------------------------------------------------------|-------------------|--------------
[`INSTALLDIR`](#installdir)                                    | n/a               | Version-dependent; [see below](#program-directory)
[`PUPPET_MASTER_SERVER`](#puppetmasterserver)                  | [`server`][s]     | `puppet`
[`PUPPET_CA_SERVER`](#puppetcaserver)                          | [`ca_server`][c]  | Value of `PUPPET_MASTER_SERVER`
[`PUPPET_AGENT_CERTNAME`](#puppetagentcertname)                | [`certname`][r]   | Value of `facter fdqn` (must be lowercase)
[`PUPPET_AGENT_ENVIRONMENT`](#puppetagentenvironment)          | [`environment`][e]| `production`
[`PUPPET_AGENT_STARTUP_MODE`](#puppetagentstartupmode)         | n/a               | `Automatic`; [see startup mode](#agent-startup-mode)
[`PUPPET_AGENT_ACCOUNT_USER`](#puppetagentaccountuser)         | n/a               | `LocalSystem`; [see agent account](#agent-account)
[`PUPPET_AGENT_ACCOUNT_PASSWORD`](#puppetagentaccountpassword) | n/a               | No Value; [see agent account](#agent-account)
[`PUPPET_AGENT_ACCOUNT_DOMAIN`](#puppetagentaccountdomain)     | n/a               | `.`; [see agent account](#agent-account)

For example:

    msiexec /qn /norestart /i puppet.msi PUPPET_MASTER_SERVER=puppet.acme.com

**Note:** If a value for the `environment` variable already exists in puppet.conf, specifying it during installation will NOT override that value.

[s]: {{puppet}}/configuration.html#server
[c]: {{puppet}}/configuration.html#caserver
[r]: {{puppet}}/configuration.html#certname
[e]: {{puppet}}/configuration.html#environment

#### `INSTALLDIR`

Where Puppet and its dependencies should be installed.

**Default:**

Puppet install type  | OS type | Default install path
-------------|--------------------|---------------------
32-bit  | 32-bit  | `C:\Program Files\Puppet Labs\Puppet`
64-bit  | 64-bit |  `C:\Program Files \Puppet Labs\Puppet`

>**Note:** Running 32-bit Puppet on 64-bit Windows is deprecated. Your Puppet installation must match your operating system's architecture.

See the **program directory** section below for information related to the program files directory.

#### `PUPPET_MASTER_SERVER`

The hostname where the Puppet master server can be reached. This will set a value for [the `server` setting][s] in the `[main]` section of [puppet.conf][].

**Default:** `puppet`

**Note:** If you set a _non-default_ value for this property, the installer will **replace** any existing value in puppet.conf. Also, the next time you upgrade, the installer will re-use your previous value for this property unless you set a new value on the command line. So if you've used this property once, you shouldn't change the `server` setting directly in puppet.conf; you should re-run the installer and set a new value there instead.

#### `PUPPET_CA_SERVER`

The hostname where the CA Puppet master server can be reached, if you are using multiple masters and only one of them is acting as the CA. This will set a value for [the `ca_server` setting][c] in the `[main]` section of [puppet.conf][].

**Default:** the value of the `PUPPET_MASTER_SERVER` property

**Note:** If you set a _non-default_ value for this property, the installer will **replace** any existing value in puppet.conf. Also, the next time you upgrade, the installer will re-use your previous value for this property unless you set a new value on the command line. So if you've used this property once, you shouldn't change the `ca_server` setting directly in puppet.conf; you should re-run the installer and set a new value there instead.

#### `PUPPET_AGENT_CERTNAME`

The node's certificate name, and the name it uses when requesting catalogs. This will set a value for [the `certname` setting][r] in the `[main]` section of [puppet.conf][].

For best compatibility, you should limit the value of `certname` to only use lowercase letters, numbers, periods, underscores, and dashes. (That is, it should match `/\A[a-z0-9._-]+\Z/`.)

**Default:** the node's fully-qualified domain name, as discovered by `facter fqdn`.

**Note:** If you set a _non-default_ value for this property, the installer will **replace** any existing value in puppet.conf. Also, the next time you upgrade, the installer will re-use your previous value for this property unless you set a new value on the command line. So if you've used this property once, you shouldn't change the `certname` setting directly in puppet.conf; you should re-run the installer and set a new value there instead.

#### `PUPPET_AGENT_ENVIRONMENT`

The node's [environment][]. This will set a value for [the `environment` setting][e] in the `[main]` section of [puppet.conf][].

**Default:** `production`

**Note:** If you set a _non-default_ value for this property, the installer will **replace** any existing value in puppet.conf. Also, the next time you upgrade, the installer will re-use your previous value for this property unless you set a new value on the command line. So if you've used this property once, you shouldn't change the `environment` setting directly in puppet.conf; you should re-run the installer and set a new value there instead.

#### `PUPPET_AGENT_STARTUP_MODE`

Whether the Puppet agent service should run (or be allowed to run). Allowed values:

* `Automatic` (**default**) --- Puppet agent will start with Windows and stay running in the background.
* `Manual` --- Puppet agent won't run by default, but can be started in the services console or with `net start` on the command line.
* `Disabled` --- Puppet agent will be installed but disabled. You will have to change its start up type in the services console before you can start the service.


#### `PUPPET_AGENT_ACCOUNT_USER`

Which Windows user account the Puppet agent service should use. This is important if Puppet agent will need to access files on UNC shares, since the default `LocalService` account cannot access these network resources.

* This user account **must already exist,** and may be a local or domain user. (The installer will allow domain users even if they have not accessed this machine before.)
* If the user isn't already a local administrator, the installer will add it to the `Administrators` group.
* The installer will also grant [`Logon as Service`](http://msdn.microsoft.com/en-us/library/ms813948.aspx) to the user.

This property should be combined with `PUPPET_AGENT_ACCOUNT_PASSWORD` and `PUPPET_AGENT_ACCOUNT_DOMAIN`. For example, to assign the agent to a domain user `ExampleCorp\bob`, you would install with:

    msiexec /qn /norestart /i puppet-<VERSION>.msi PUPPET_AGENT_ACCOUNT_DOMAIN=ExampleCorp PUPPET_AGENT_ACCOUNT_USER=bob PUPPET_AGENT_ACCOUNT_PASSWORD=password

**Default:** `LocalSystem`

#### `PUPPET_AGENT_ACCOUNT_PASSWORD`

The password to use for Puppet agent's user account. See the notes about users above.

**Default:** no value.

#### `PUPPET_AGENT_ACCOUNT_DOMAIN`

The domain of Puppet agent's user account. See the notes about users above.

**Default:** `.`

## Upgrading

Refer to [Upgrading Windows agents](./upgrade_windows.html) for instructions.

## Uninstalling

To uninstall Puppet, use the Windows *Add or Remove Programs* interface, or you can uninstall from the command line.

To uninstall from the command line, you must have the original .msi file or know the <a href="http://msdn.microsoft.com/en-us/library/windows/desktop/aa370854(v=vs.85).aspx">ProductCode</a> of the installed MSI:

    msiexec /qn /norestart /x [puppet.msi|product-code]

Uninstalling will remove Puppet's program directory, the Puppet agent service, and all related registry keys. It will leave the [data directory](#data-directory) intact, including any SSL keys. To completely remove Puppet from the system, the data directory can be manually deleted.


## Installation details

### What gets installed

In order to provide a self-contained installation, the Puppet installer includes all of Puppet's dependencies, including Ruby, Gems, and Facter. (Puppet redistributes the 32-bit Ruby application from [rubyinstaller.org](http://rubyinstaller.org). It also distributes 64-bit Ruby.) MCollective is also installed.

These prerequisites are used only for Puppet Enterprise components and do not interfere with other local copies of Ruby.


### Program directory

Unless they are overridden during installation, PE and its dependencies are installed into the following default installation paths:


Puppet install type  | OS type | Default install path
-------------|--------------------|---------------------
32-bit  | 32-bit  | `C:\Program Files\Puppet Labs\Puppet`
64-bit  | 64-bit |  `C:\Program Files \Puppet Labs\Puppet`

>**Note:** Running 32-bit Puppet on 64-bit Windows is deprecated. Your Puppet installation must match your operating system's architecture.

You can locate the Program Files directory using the `PROGRAMFILES` variable or the `PROGRAMFILES(X86)` variable. See “Default install path” in the table above to determine which variable to use.


Puppet's program directory contains the following subdirectories:

Directory           | Description
--------------------|--------------------------------------
bin                 | scripts for running Puppet and Facter
facter              | Facter source
hiera               | Hiera source
mcollective         | MCollective source
misc                | resources
puppet              | Puppet source
service             | code to run Puppet agent as a service
sys                 | Ruby and other tools

### Agent startup mode

The agent is set to `Automatic` startup by default, but allows for you to pass `Manual` or `Disabled` as well.

* `Automatic` means that the Puppet agent will start with Windows and be running all the time in the background. This is what you would choose when you want to run Puppet with a master.
* `Manual` means that the agent will start up only when it is started in the services console or through `net start` on the command line. Typically this used in advanced usages of Puppet.
* `Disabled` means that the agent will be installed but disabled and will not be able to start in the services console (unless you change the start up type in the services console first). This is desirable when you want to install Puppet but you only want to invoke it as you specify and not use it with a master.

### Agent account

By default, Puppet installs the agent with the built in `SYSTEM` account. This account does not have access to the network, therefore it is suggested that another account that has network access be specified. The account must be an existing account. In the case of a domain user, the account does not need to have accessed the box. If this account is not a local administrator and it is specified as part of the install, it will be added to the `Administrators` group on that particular node. The account will also be granted [`Logon as Service`](http://msdn.microsoft.com/en-us/library/ms813948.aspx) as part of the installation process.

As an example, if you wanted to set the agent account to a domain user `AbcCorp\bob` you would run the installer from the command line appending the following items: `PUPPET_AGENT_ACCOUNT_DOMAIN=AbcCorp PUPPET_AGENT_ACCOUNT_USER=bob PUPPET_AGENT_ACCOUNT_PASSWORD=password`.

### Data directory

Puppet Enterprise and its components store settings (`puppet.conf`), manifests, and generated data (such as logs and catalogs) in the **data directory.** Puppet's data directory contains two subdirectories for the various components (facter, MCollective, etc.):

* `etc` (the `$confdir`): Contains configuration files, manifests, certificates, and other important files.
* `var` (the `$vardir`): Contains generated data and logs.

When run with elevated privileges --- Puppet's intended state --- the data directory is located in the [`COMMON_APPDATA`](http://msdn.microsoft.com/en-us/library/aa367992(v=vs.85).aspx) folder. This folder is located at `%PROGRAMDATA%\PuppetLabs\`, which is usually `C:\ProgramData\PuppetLabs\`.

Since the CommonAppData directory is a system folder, it is hidden by default. See <http://support.microsoft.com/kb/812003> for steps to show system and hidden files and folders.

If Puppet is run without elevated privileges, it uses a `.puppet` directory in the current user's home folder as its data directory. This may result in Puppet having unexpected settings.
