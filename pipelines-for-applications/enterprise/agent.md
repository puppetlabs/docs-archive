---
layout: default
title: "The Pipelines agent"
--- 

The Pipelines agent runs on your servers, where it provides several pieces of functionality, including:

* [Pipelines CLI](./cli.html)
* Pipelines deploys
* [Private builds](./server.html)
* [Key management server](./application-secure.html)

Pipelines uses an agent model to provide server communication. The Pipelines agent can be installed on any server in any network, cloud, or provider. 

After installation and authentication, the agent makes an encrypted connection back to the Pipelines SaaS. This simplifies providing access to servers from Puppet Pipelines, with no need for SSH key management.

There are multiple methods of installing the Pipelines agent:

* [Install the agent manually](./agent.html#installing-the-pipelines-agent) 
* [Install the agent automatically](./distelliyml.html) 
* [Provision EC2 instances](./server-type.html) to auto-install the agent

## Minimum system requirements

System requirements for the build server on which you install the agent vary based on the expected level of activity your build server will receive. 

At a minimum, the build server requires:

* Two CPUs
* Four GB of RAM
* 100 GB of disk space (200 GB for a shared build server)

## Install Pipelines agent on *nix or mac OS

The Pipelines agent connects your destination server to Pipelines for Applications, enabling application deployments. The agent is also required if you use Puppet Pipelines to build on your own servers. For more information see [Using your own build server](./server.html).

> **Note:** Installation requires root (administrator) permissions.

1. **Optional.** By default, the Pipelines agent is installed at `/usr/local/bin`. If you wish to install the agent elsewhere, use the `DISTELLI_INSTALL_DIR` environment variable to specify your desired installation path.

   ~~~
   export DISTELLI_INSTALL_DIR=/<PATH_TO_INSTALL_DIRECTORY>
   ~~~
   
1. Make sure that your `$PATH` environment variable is configured correctly and points to either `/usr/local/bin` or the installation location you chose in step 1. 

1. Download the Pipelines agent and install the CLI by using either wget or curl. 

   ~~~
   wget -qO- https://pipelines.puppet.com/download/client | sh
   ~~~

   ~~~
   curl -sSL https://pipelines.puppet.com/download/client | sh
   ~~~
   
1. To complete installation of the agent, run the following Pipelines CLI command:

   ~~~
   distelli agent install
   ~~~

   At the `Team [2]:` prompt, enter `1`.

   ~~~
   ServerA:~$ wget -qO- https://pipelines.puppet.com/download/client | sh
   This script requires superuser privileges to install packages
   Please enter your password at the sudo prompt

   [sudo] password for bmcgehee: 
	   Installing Distelli CLI 3.51 for architecture 'Linux-x86_64'...
	   Downloading https://s3.amazonaws.com/download.distelli.com/distelli.Linux-x86_64/distelli.Linux-x86_64-3.51.gz
   To install the agent, run:
	   sudo /usr/local/bin/distelli agent install
   ServerA:~$ sudo /usr/local/bin/distelli agent install
   Distelli Email: jdoe@distelli.com
		 Password: 
	   1: User: jdoe
	   2: Team: janedoe/TeamJane
   Team [2]: 1
   Server Info: https://www.distelli.com/jdoe/servers/12345678-4765-ac42-bd7a-080027c8277c
   Starting upstart daemon with name:	dtk-supervise-cc123456787ad94a8d34ac610381242f9ae28bb8
   ~~~

> **Important:** If you enter your account password incorrectly too many times, a `Failed to login to your Distelli Account, Please check your credentials` message will appear, and your account will be locked as a security precaution. The lock expires in two hours. 

## Install Pipelines agent on Windows

The Pipelines agent connects your destination server to Pipelines for Applications, enabling application deployments. The agent is also required if you use Puppet Pipelines to build on your own servers. For more information see [Using your own build server](./server.html).

> **Note:** Installation requires root (administrator) permissions.

1. **Optional.** By default, the Pipelines agent is installed at `%ProgramFiles%/Distelli`. If you wish to install the agent elsewhere, use the `DISTELLI_INSTALL_DIR` environment variable to specify your desired installation path.

   ~~~
   set DISTELLI_INSTALL_DIR=C:\<PATH_TO_INSTALL_DIRECTORY>
   ~~~
   
1. Make sure that your `%PATH%` environment variable is configured correctly and points to either `%ProgramFiles%/Distelli` or the installation location you chose in step 1. 

1. Copy the following PowerShell command into a command window.

   ~~~
   powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((new-object net.webclient).DownloadString('https://pipelines.puppet.com/download/client.ps1'))" & SET PATH=%PATH%;%ProgramFiles%/Distelli
   ~~~

1. To complete installation of the agent, run the following Pipelines CLI command:

   ~~~
   distelli agent install
   ~~~

   At the `Team [2]:` prompt, enter `1`.

   ~~~
   ServerA:~$ wget -qO- https://pipelines.puppet.com/download/client | sh
   This script requires superuser privileges to install packages
   Please enter your password at the sudo prompt

   [sudo] password for bmcgehee: 
	   Installing Distelli CLI 3.51 for architecture 'Linux-x86_64'...
	   Downloading https://s3.amazonaws.com/download.distelli.com/distelli.Linux-x86_64/distelli.Linux-x86_64-3.51.gz
   To install the agent, run:
	   sudo /usr/local/bin/distelli agent install
   ServerA:~$ sudo /usr/local/bin/distelli agent install
   Distelli Email: jdoe@distelli.com
		 Password: 
	   1: User: jdoe
	   2: Team: janedoe/TeamJane
   Team [2]: 1
   Server Info: https://www.distelli.com/jdoe/servers/12345678-4765-ac42-bd7a-080027c8277c
   Starting upstart daemon with name:	dtk-supervise-cc123456787ad94a8d34ac610381242f9ae28bb8
   ~~~

> **Important:** If you enter your account password incorrectly too many times, a `Failed to login to your Distelli Account, Please check your credentials` message will appear, and your account will be locked as a security precaution. The lock expires in two hours. 

## Validate installation

To validate that the agent is installed and working use the `distelli agent status` command.

> **Note:** This command requires root (administrator) access.

~~~
distelli agent status
Distelli Agent (serverA) is Running with id 766b88c8-e925-11e4-ae8b-080027cc07f7
~~~

## Installation options

The `distelli agent install` command can be used to change the agent behavior. You can specify install options using the following syntax: 

~~~
distelli agent install [-nostart] [-onboot] [-conf <FILE>]
~~~

| Option | Description | 
| --- | --- | 
| `-nostart` | Installs the agent, but does not start it. Manual action is required to start the agent. |
| `-onboot` | Installs the agent, but does not start it. The agent will automatically start after the next reboot.
| `-conf <FILE>` | Instructs the agent to read a `distelli.yml` file for the agent access token and secret key. See the [distelli.yml usage guide](./distelliyml.html) for more information. | 

If you issue this command on a server that has running deployed software, it will continue to run. If you specify the `-nostart` option, the applications will not continue to run after a boot of the server. Doing a `distelli agent install` or `distelli agent start` will (re)start the deployed applications.

> **Note:** The above comments assume the application running was started with the [Exec section of the distelli-manifest.yml](./manifest.html).

### Install a specific version of the agent 

You can install a specific version of the Pipelines agent with the following syntax, replacing `#.##.##` with your desired version number. 

~~~
wget -qO- https://pipelines.puppet.com/download/client/#.##.## | sh
~~~

~~~
curl -sSL https://pipelines.puppet.com/download/client/#.##.## | sh
~~~

~~~
powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((new-object net.webclient).DownloadString('https://pipelines.puppet.com/download/client/#.##.##.ps1'))" & SET PATH=%PATH%;%ProgramFiles%/Distelli
~~~

## Upgrade the agent

To upgrade the Pipelines agent, reinstall the agent on top of the existing agent. 

> **Note:** During the upgrade you can specify [install options](#installation-options).

## Install the agent without sudo access

If necessary, you can install the agent without `sudo`. It's important to note, however, that if you do this, users doing Pipelines builds, restarts, terminate, and/or deploy tasks on the server will not have access to run `sudo` commands.

1. Create a user for the Pipelines agent to run as. In a normal install, this user defaults to `distelli`.

   ~~~
   useradd -m distelli
   ~~~

	You can change the user name to whatever you like, but this document will continue to refer to the user as "Distelli".

1. Create a directory for the Pipelines agent executable. If you wish users to have access to this command, you may want to add this to the path. The Distelli user must have write access to this directory.

   ~~~
   mkdir /home/distelli/bin
   ~~~

	In a normal install, this defaults to `/usr/local/bin`.

1. Set the Pipelines agent executable directory environment variable. Setting this tells the install script where to place the executable. This directory was created in the previous step.

   ~~~
   export DISTELLI_INSTALL_DIR=/home/distelli/bin
   ~~~

	In a normal install, this is unnecessary.

1. Add a working directory for Pipelines to use for builds and deploys. This is the directory that the Pipelines agent will use for executing builds, deploys, and logging. The Distelli user must own this directory.

   ~~~
   mkdir /home/distelli/distelli
   ~~~

	The Unix owner of this directory will be the user that the Pipelines agent runs as. In a normal install, this directory is `/distelli`.

1. Add a directory for startup scripts. The Pipelines agent needs to be automatically started on server reboot. If you are installing the agent without `sudo` access, you will need to manually update the servers init scripts from these. The Distelli user must have write access to this directory.

	Create a directory for Pipelines to create the init scripts in.

   ~~~
   mkdir /home/distelli/sysv
   ~~~

1. Switch to the Distelli user. To run the Pipelines agent install, use the Distelli user. Either log out and log in as this user, or use `su` (Switch User).

   ~~~
   su - distelli
   ~~~

1. Download the Pipelines agent install script. This script is dependent on the environment variable `DISTELLI_INSTALL_DIR`, set above. If you just logged in as the Distelli user, you will have to re-set the `DISTELLI_INSTALL_DIR` environment variable.

   ~~~
   wget -qO- https://pipelines.puppet.com/download/client | sh
   ~~~

	If you are prompted for `sudo` access in the above command, this is typically a permissions issue indicating that the Distelli user doesn't have permission to access the directory `DISTELLI_INSTALL_DIR`, or the environment variable is not set.

1. Install the agent. You will have to specify directories created above as options in the command. Here is an example:

   ~~~
   /home/distelli/bin/distelli agent install -data-dir /home/distelli/distelli -manager sysv -manager-dir /home/distelli/sysv
   ~~~

1. Copy the init scripts. Finally, look in the `/home/distelli/sysv` directory for the server init scripts. These scripts need to be moved to their appropriate place so that when the server reboots, the Pipelines agent is restarted.

## Run the agent as a different user

If you need to run the Pipelines agent as a different user without using `sudo`, set up a new user account and install the agent for that user. 

> **Important:** This procedure works only on *nix systems.

1. Create a new account for the user you wish to use to run the agent. We'll use the username `testuser` in this walkthrough. 

   ~~~
   useradd -m testuser
   ~~~
   
1. Create the data directory location:

   ~~~
   mkdir /distelli
   ~~~

   > **Tip:** You can change the location of this directory when running the `agent install` command in step 5.
   
1. Set the new user as the owner of the data-dir:

   ~~~
   chown testuser:testuser /distelli/
   ~~~
   
1. Install the Pipelines agent using either wget or curl:

   ~~~
   wget -qO- https://pipelines.puppet.com/download/client | sh
   ~~~

   ~~~
   curl -sSL https://pipelines.puppet.com/download/client | sh
   ~~~

1. Complete the installation by running:

   ~~~
   /usr/local/bin/distelli agent install
   ~~~

   If you wish to specify an alternative base directory for the agent, use `-data-dir` when running the `agent install` command:
   
   ~~~
   /usr/local/bin/distelli agent install -data-dir <DIRECTORY>
   ~~~



