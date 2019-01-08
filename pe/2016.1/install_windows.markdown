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
[basic]: ./windows_basic_concepts.html

> ![windows logo](./images/windows-logo-small.jpg) This section refers to Windows functionality. To install PE on \*nix nodes, see [Installing Puppet Enterprise](./install_basic.html).

For supported versions of Windows, [see the system requirements page](./install_system_requirements.html#operating-system).

Windows nodes in Puppet Enterprise:

* Can fetch configurations from a Puppet master and apply manifests locally.
* Can respond to orchestration commands.
* Cannot serve as a Puppet master, console, or database support server.

>**Note:** Puppet *must* be run with elevated privileges (Select *Run as administrator* when opening Command Prompt).


## Installing Windows Puppet Enterprise agents


The PE Windows installer is a standard Windows .msi package and runs as a graphical wizard.

The installer must be run with elevated privileges. Installing Puppet **does not** require a system reboot.

1. [Download][downloadpe] and run the installer.

2. When prompted by the install dialog, provide the hostname of your Puppet master server.

   ![Puppet master hostname selection][server]

3. Load a list of currently pending node requests by clicking **Nodes** > **Unsigned certificates**.

4. Click *Accept All* to approve the requests and add the node.

> The Puppet agent can now retrieve configurations from the master the next time Puppet runs.

### After installation

* Puppet agent is running as a Windows service, and by default will fetch and apply configurations every 30 minutes. You can now assign classes to the node as normal; see [Assigning configurations to nodes](./puppet_assign_configurations.html) for more details. After the first Puppet run, the MCollective service will also be running and the node can now be controlled with MCollective. The Puppet agent service and the MCollective service can be started and stopped independently using either the service control manager UI or the command line `sc.exe` utility; see [Running Puppet on Windows]({{puppet}}/services_commands_windows.html) for more details.

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

>**Note**: If you’re installing on a 32-bit OS, you have to run 32-bit Puppet. If you’re installing on a 64-bit OS, you can run either 32 or 64-bit Puppet. However, if you’re installing Puppet on Windows 2003, then you must install 32-bit Puppet, even if it’s a 64-bit version of Windows.

**Default:**

Puppet install type  | OS type | Default install path
-------------|--------------------|---------------------
32-bit  | 32-bit  | `C:\Program Files\Puppet Labs\Puppet`
32-bit  | 64-bit  | `C:\Program Files (x86)\Puppet Labs\Puppet`
64-bit  | 64-bit |  `C:\Program Files \Puppet Labs\Puppet`



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

To upgrade Puppet, download and install a new version of the .msi package. No extra steps are required, the installer will handle stopping and re-starting the Puppet agent service.

When upgrading, the installer does not replace any settings in the main puppet.conf configuration file, but it can add previously unspecified settings if they are provided on the command line.

>**Note**: You can use a [module to upgrade your Puppet agent](https://forge.puppetlabs.com/puppetlabs/puppet_agent) if you're upgrading from Puppet Enterprise 3.8. For more information, see [upgrading agents](./install_upgrading.html#upgrade-agents).

The following version upgrades are supported:

* 32-bit to PE 2015.2 32-bit. Program data is the same, for example c:\programData\Puppet Labs
* 32-bit PE to 64-bit PE. You can only do this upgrade from PE 3.8 to PE 2015.2. This upgrade changes the default so it goes from:

c:\Program Files(86)\ to c:\Program Files \

If you have installed into a custom location, that location will not be rerouted.

## Switching to an older version of PE

If you’re downgrading from PE 2015.2 to an older version, we recommend that you uninstall PE 2015.2 first, and then install the version of your choice. This guarantees the cleanest install.

You can also use the Windows .msi to switch versions. It will add PE to the system path, with the following caveats:

* Processes that were already running will not see the change until they are restarted.
* Puppet will expand your variables. This is a problem if the path looks like this: `PATH = %SystemRoot\System32`. Puppet will change this to `PATH = c:\Windows\System32`. This is a known issue.
* In some cases, files from the newer version will be left on your machine.


## Uninstalling

To uninstall Puppet, use the Windows *Add or Remove Programs* interface, or you can uninstall from the command line.

To uninstall from the command line, you must have the original .msi file or know the <a href="http://msdn.microsoft.com/en-us/library/windows/desktop/aa370854(v=vs.85).aspx">ProductCode</a> of the installed MSI:

    msiexec /qn /norestart /x [puppet.msi|product-code]

Uninstalling will remove Puppet's program directory, the Puppet agent service, and all related registry keys. It will leave the [data directory](#data-directory) intact, including any SSL keys. To completely remove Puppet from the system, the data directory can be manually deleted.


## Installation details

### What gets installed

In order to provide a self-contained installation, the Puppet installer includes all of Puppet's dependencies, including Ruby, Gems, and Facter. (Puppet redistributes the 32-bit Ruby application from [rubyinstaller.org](http://rubyinstaller.org). It also distributes 64-bit Ruby with Puppet 3.8.x and going forward.) MCollective is also installed.

These prerequisites are used only for Puppet Enterprise components and do not interfere with other local copies of Ruby.


### Program directory

Unless they are overridden during installation, PE and its dependencies are installed into the following default installation paths:


Puppet install type  | OS type | Default install path
-------------|--------------------|---------------------
32-bit  | 32-bit  | `C:\Program Files\Puppet Labs\Puppet`
32-bit  | 64-bit  | `C:\Program Files (x86)\Puppet Labs\Puppet`
64-bit  | 64-bit |  `C:\Program Files \Puppet Labs\Puppet`


You can locate the Program Files directory using the `PROGRAMFILES` variable or the `PROGRAMFILES(X86)` variable. See “Default install path” in the table above to determine which variable to use.

>**Note**: As mentioned, if you’re installing on a 32-bit OS you have to run 32-bit Puppet. If you’re installing on a 64-bit OS, you can run either 32 or 64-bit Puppet.


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