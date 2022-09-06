# Installing PE client tools

PE client tools are a set of command line tools that let you access Puppet Enterprise services from a workstation that is not necessarily managed by Puppet.

The `pe-client-tools` package is included in the PE installation tarball. When you install, the client tools are automatically installed on the same node as the master.

Client tools versions align with PE versions. For example, if you're running PE 2017.3, you should use the 2017.3 client tools. In some cases, we might issue patch releases \("x.y.z"\) for PE or the client tools. You don't need to match patch numbers between PE and the client tools. Only the "x.y" numbers need to match.

**Important:** When you upgrade PE to a new "x.y" version, install the appropriate "x.y" version of PE client tools.

The package includes client tools for these services:

-   Orchestrator — Allow you to control the rollout of changes in your infrastructure, and provides the interface to the orchestration service. Tools include `puppet-job` and `puppet-app`.
-   Puppet access — Authenticates you to the PE RBAC token-based authentication service so that you can use other capabilities and APIs.
-   Code Manager — Provides the interface for the Code Manager and file sync services. Tools include `puppet-code`.
-   PuppetDB CLI — Enables certain operations with PuppetDB, such as building queries and handling exports.

Because you can safely run these tools remotely, you no longer need to SSH into the master to execute commands. Your permissions to see information and to take action are controlled by PE role-based access control. Your activity is logged under your username rather than under `root` or the `pe-puppet` user.

**Related information**  


[Orchestrator configuration files](configuring_puppet_orchestrator.md#)

[Configuring puppet-access](rbac_token_auth_intro.md#)

[Installing and configuring puppet-code](puppet_code.md#)

## Supported PE client tools operating systems

The PE client tools package can be installed on these platforms.

|Operating system|Versions|Arch|
|----------------|--------|----|
|Red Hat Enterprise Linux|6, 7|x86\_64|
|CentOS|6, 7|x86\_64|
|Oracle Linux|6, 7|x86\_64|
|Scientific Linux|6, 7|x86\_64|
|SUSE Linux Enterprise Server|11, 12|x86\_64|
|Ubuntu|14.04. 16.04|amd64|
|Windows \(Consumer OS\)|7, 8.1, 10|x86, x64|
|Windows Server \(Server OS\)|2008

|x86, x64|
|2008r, 22012, 2012r2, 2012r2 core

|x64|
|2016| |
|macOS|10.10, 10.11, 10.12, 10.13| |

## Install PE client tools on a managed workstation

To use the client tools on a system other than the master, where they're installed by default, you can install the tools on a controller node.

### Before you begin

Controller nodes must be running the same OS as your master and must have an agent installed.

### Procedure

1.  In the console, create a controller classification group, for example PE Controller, and ensure that its **Parent name** is set to **All Nodes**.

2.  Select the controller group and add the **puppet\_enterprise::profile::controller** class.

3.  Pin the node that you want to be a controller to the controller group.

    1.  In the controller group, on the **Rules** tab, in the **Certname** field, enter the certname of the node.

    2.  Click **Pin node** and commit changes.

4.  Run Puppet on the controller machine.


**Related information**  


[Create classification node groups](grouping_and_classifying_nodes.md#)

## Install PE client tools on an unmanaged workstation

You can install the `pe-client-tools` package on any workstation running a supported OS. The workstation OS does not need to match the master OS.

### Before you begin

Review prerequisites for timekeeping, name resolution, and firewall configuration, and ensure that these ports are available on the workstation.

-   **8143** — The orchestrator client uses this port to communicate with orchestration services running on the master.
-   **4433 —**The Puppet access client uses this port to communicate with the RBAC service running on the master.
-   **8170 —**If you use the Code Manager service, it requires this port.

### Install PE client tools on an unmanaged Linux workstation

#### Procedure

1.  On the workstation, create the directory `/etc/puppetlabs/puppet/ssl/certs`.

2.  On the master, navigate to `/etc/puppetlabs/puppet/ssl/certs/` and copy `ca.pem` to the directory you created on the workstation.

3.  On the workstation, make sure file permissions are correct: `chmod 444 /etc/puppetlabs/puppet/ssl/certs/ca.pem`

4.  Verify that the checksum of `ca.pem` on the workstation matches the checksum of the same file on the master.

5.  Download the [pe-client-tools package](https://puppet.com/download-puppet-enterprise-client-tools) for the platform appropriate to your workstation.

6.  Unpack the tarball and navigate to the `packages/<PLATFORM>` directory.

7.  Use your workstation's package management tools to install the pe-client-tools.

    For example,on RHEL platforms: `rpm -Uvh pe-client-tools-<VERSION-and-PLATFORM>.rpm`


### Install PE client tools on an unmanaged Windows workstation

You can install the client tools on a Windows workstation using the setup wizard or the command line.

#### About this task

To start using the client tools on your Windows workstation, open the **PE ClientTools Console** from the **Start** menu.

#### Procedure

1.  On the workstation, create the directory `C:\ProgramData\PuppetLabs\puppet\etc\ssl\certs`.

    For example: `mkdir C:\ProgramData\PuppetLabs\puppet\etc\ssl\certs`

2.  On the master, navigate to `/etc/puppetlabs/puppet/ssl/certs/` and copy `ca.pem` to the directory you created on the workstation.

3.  On the workstation, make sure the file permissions are set to read-only for `C:\ProgramData\PuppetLabs\puppet\etc\ssl\certs\ca.pem`.

4.  Verify that the checksum of `ca.pem` on the workstation matches the checksum of the same file on the master.

5.  Install the client tools using guided setup or the command line.

    -   Guided setup
        1.  Download the Windows [pe-client-tools-package](https://puppet.com/download-puppet-enterprise-client-tools).

        2.  Double-click the `pe-client-tools` .msi file.

        3.  Follow prompts to accept the license agreement and select the installation location.

        4.  Click **Install**.

    -   Command line
        1.  Download the Windows [pe-client-tools-package](https://puppet.com/download-puppet-enterprise-client-tools).

        2.  From the command line, run the installer:

            ```
            msiexec /i <PATH TO PE-CLIENT-TOOLS.MSI> TARGETDIR="<INSTALLATION DIRECTORY>"
            ```

            `TARGETDIR` is optional.


### Install PE client tools on an unmanaged macOS workstation

You can install the client tools on a macOS workstation using Finder or the command line.

#### About this task

#### Procedure

1.  On the workstation, create the directory `/etc/puppetlabs/puppet/ssl/certs`.

2.  On the master, navigate to `/etc/puppetlabs/puppet/ssl/certs/` and copy `ca.pem` to the directory you created on the workstation.

3.  On the workstation, make sure file permissions are correct: `chmod 444 /etc/puppetlabs/puppet/ssl/certs/ca.pem`

4.  Verify that the checksum of `ca.pem` on the workstation matches the checksum of the same file on the master.

5.  Install the client tools using Finder or the command line.

    -   Finder
        1.  Download the macOS [pe-client-tools-package](https://puppet.com/download-puppet-enterprise-client-tools).

        2.  Open the `pe-client-tools` .dmg and click the `installer` .pkg.

        3.  Follow the prompts to install the client tools.

    -   Command line
        1.  Download the macOS [pe-client-tools-package](https://puppet.com/download-puppet-enterprise-client-tools).

        2.  Mount the disk image: `sudo hdiutil mount <DMGFILE>`.

            A line appears ending with `/Volumes/puppet-agent-VERSION`. This directory location is the mount point for the virtual volume created from the disk image.

        3.  Run `cd /Volumes/pe-client-tools-VERSION`.
        4.  Run `sudo installer -pkg pe-client-tools-<VERSION>-installer.pkg -target /`.
        5.  Run `cd ~` and then run `sudo umount /Volumes/pe-client-tools-VERSION`.

## Configuring and using PE client tools

Use configuration files to customize how client tools communicate with the master.

For each client tool, you can create config files for individual machines \(global\) or for individual users. Configuration files are structured as JSON.

Save configuration files to these locations:

-   Global

    -   \*nix — `/etc/puppetlabs/client-tools/`

    -   Windows —`%ProgramData%\puppetlabs\client-tools`

-   User
    -   \*nix — `~/.puppetlabs/client-tools/`

    -   Windows —`%USERPROFILE%\.puppetlabs\client-tools`


On managed client nodes where the operating system and architecture match the master, you can have PE manage Puppet code and orchestrator global configuration files using the `puppet_enterprise::profile::controller` class.

For example configuration files and details about using the various client tools, see the documentation for each service.

|Client tool|Documentation|
|-----------|-------------|
|Orchestrator|-   [Deploying applications with Puppet Application Orchestration: workflow](deploying_applications_workflow.md#)
-   [Running jobs with Puppet orchestrator](running_jobs_with_puppet_orchestrator_overview.md#)
-   [Running Puppet on demand from the CLI](running_puppet_on_demand_from_the_cli.md#)
-   [Running tasks from the command line](running_tasks_from_the_command_line.md#)
-   [Review jobs from the command line](reviewing_jobs_on_the_cli.md#)

|
|Puppet access|-   [Token-based authentication](rbac_token_auth_intro.md#)

|
|Puppet code|-   [Triggering Code Manager on the command line](puppet_code.md#)

|
|PuppetDB|-   [PuppetDB CLI](https://puppet.com/docs/puppetdb/5.2/pdb_client_tools.html)

|

**Related information**  


[Orchestrator configuration files](configuring_puppet_orchestrator.md#)

[Configuring puppet-access](rbac_token_auth_intro.md#)

[Installing and configuring puppet-code](puppet_code.md#)

