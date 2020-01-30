---
layout: default
title: "The Pipelines agent"
--- 

Pipelines uses an agent model to provide server communication. 

The Pipelines agent can be installed on any server in any network, cloud, or provider, and supports *nix and Windows operating systems. The agent provides you with a command-line interface for Pipelines. It also manages Pipelines deployments and builds, and is required for private build, and key management servers.

Upon installing and authenticating the Pipelines agent, the agent will make an encrypted connection back to the Pipelines SaaS. This simplifies providing access to servers from Puppet Pipelines, because there's no need for SSH key management.

Within Pipelines, you can provision Amazon EC2 instances that will auto-install the Pipelines agent. For more information see <a href="https://puppet.com/docs/pipelines-for-apps/free/server-type.html" target="_blank">Pipelines for Applications with GCE and EC2 servers</a>. You can also use the `distelli.yml` file for [automated installation and authentication](./distelliyml.html) of the agent on any server in any network, cloud, or provider.

## Install the Pipelines agent

To set up the agent on your server:
1. Download and install the agent:
   > **Important**: Installing the Pipelines agent requires administrator (root) privileges.
    
   * On Linux and macOS:
     1. Download the agent using curl or wget:
   
        ```sudo curl -sSL https://pipelines.puppet.com/download/client | sh```

        ```wget -qO- https://pipelines.puppet.com/download/client | sh```
   
     1. Install the agent:
   
        ```sudo /usr/local/bin/distelli agent install```

   * On Windows, use the following command to download and install the agent:
        
        ```powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((new-object net.webclient).DownloadString('https://pipelines.puppet.com/download/client.ps1'))" & SET PATH=%PATH%;%ProgramFiles%/Distelli```
1. When prompted, use your Pipelines credentials to log in. The agent will begin automatically. 
1. (Optional) Check to see if the agent is running:

   ```sudo distelli agent status```
1. Make distelli a member of the docker group. This gives Pipelines the correct permissions to run Docker builds on your server. 
   
   ```sudo usermod -aG docker distelli```
   
1. Restart the agent to make sure that your changes take effect.
sudo distelli agent restart

   ```sudo distelli agent restart```

<h3><a name="installation-options"></a>Installation options</h3>

Install and possibly start (or restart) the agent.

<h4>SYNTAX</h4>

~~~
/usr/local/bin/distelli agent install [-nostart] [-onboot] [-conf FILE]
~~~

<h4>OPTIONS</h4>

~~~
-nostart
~~~

This will install the agent, but not start it. Manual action will be required to start the agent.

~~~
-onboot
~~~

This will install the agent, but not start it. The agent will automatically start after the next reboot.

~~~
-conf FILE
~~~

This option tells the agent to read a distelli.yml for the agent access token and secret key. See the [distelli.yml usage guide](./distelliyml.html) for more information.

<h4>USAGE</h4>

The `/usr/local/bin/distelli agent install` command can be used to change the agent behavior. Using this command you can specify install options, as noted above.
If you issue this command on a server that has running deployed software, it will continue to run. If you specify the `-nostart` option, the applications will not continue to run after a boot of the server. Doing a `/usr/local/bin/distelli agent install` or `/usr/local/bin/distelli agent start` will (re)start the deployed applications.

> **Note:** The above comments assume the application running was started with the Exec section of the distelli-manifest.yml. For more information see [Pipelines for Applications Manifests](https://puppet.com/docs/pipelines-for-apps/free/manifest.html).

<h4>EXAMPLES</h4>

Reinstall the agent and tell it not to start until next boot.

~~~
/usr/local/bin/distelli agent install -onboot
~~~

<h2><a name="upgrading-the-agent"></a>Upgrading the agent</h2>

To upgrade the Pipelines agent, re-install the agent on top of the existing agent. This will perform an upgrade.

> **Note:** During the upgrade you can specify [install options](#installation-options).


<h2><a name="install-a-specific-agent-version"></a>Install a specific agent version</h2>

You can install earlier versions of the Pipelines agent with one of the following syntaxes.

<h4>wget syntax</h4>

~~~
wget -qO- https://pipelines.puppet.com/download/client/#.##.## | sh
~~~

<h4>curl syntax</h4>

~~~
curl -sSL https://pipelines.puppet.com/download/client/#.##.## | sh
~~~

<h4>Example</h4>

~~~
curl -sSL https://pipelines.puppet.com/download/client/3.62.14 | sh
~~~

<h4>Windows</h4>

~~~
powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((new-object net.webclient).DownloadString('https://pipelines.puppet.com/download/client/#.##.##.ps1'))" & SET PATH=%PATH%;%ProgramFiles%/Distelli
~~~

<h2><a name="install-the-agent-non-sudo"></a>Install the agent without sudo access</h2>

By default, the Pipelines agent will want to use sudo to install itself. You can install the agent without sudo. It's important to note, however, that if you do this, users doing Pipelines builds, restarts, terminate, and/or deploy tasks on the server will not have access to run sudo commands.

Here are the steps to install the Pipelines agent without sudo:

1. Create a user for the Pipelines agent to run as.

	In a normal install, this user defaults to <b>distelli</b>.

   ~~~
   useradd -m distelli
   ~~~

	You can change the user name to whatever you like, but this document will continue to refer to the user as "distelli".

1. Create a directory for the Pipelines agent executable.

	This directory will house the "distelli" agent executables. If you wish users to have access to this command, you may want to add this to the path.

	The distelli user must have write access to this directory.

   ~~~
   mkdir /home/distelli/bin
   ~~~

	In a normal install, this defaults to <b>/usr/local/bin</b>.

1. Set the Pipelines agent Executable Directory Environment Variable

	Setting this tells the install script where to place the executable. This directory was created in the previous step.

   ~~~
   export DISTELLI_INSTALL_DIR=/home/distelli/bin
   ~~~

	In a normal install, this is unnecessary.

1. Add a Working Directory for Pipelines to do Builds and Deploys

	This is the directory that the Pipelines agent will use for executing builds, deploys, and logging.

	The distelli user must own this directory.

   ~~~
   mkdir /home/distelli/distelli
   ~~~

	The unix owner of this directory will be the user that the Pipelines agent runs as.

	In a normal install, this directory is <b>/distelli</b>.

1. Add a Directory for Startup Scripts

	The Pipelines agent needs to be automatically started on server reboot. If you are installing the agent without sudo access, you will need to manually update the servers init scripts from these.

	The Distelli user must have write access to this directory.

	Create a directory for Pipelines to create the init scripts in.

   ~~~
   mkdir /home/distelli/sysv
   ~~~

1. Switch User to the distelli User

	To run the Pipelines agent install, use the distelli user. Either log out and login as this user, or <b>su</b> (Switch User) to the user.

   ~~~
   su - distelli
   ~~~

1. Download the Pipelines Agent Install Script

	In this step you will download the install script. This script is dependent on the environment variable DISTELLI_INSTALL_DIR, set above. If you just logged in as the Distelli user, you will have to re-set the DISTELLI_INSTALL_DIR environment variable.

   ~~~
   wget -qO- https://pipelines.puppet.com/download/client | sh
   ~~~

	If you are prompted for sudo access in the above command, this is typically a permissions issue that the distelli user doesn't have permission to the directory DISTELLI_INSTALL_DIR or the environment variable is not set.

1. Install the Agent

	When installing the agent, you will have to specify directories created above as options in the command. Here is an example:

   ~~~
   /home/distelli/bin/distelli agent install -data-dir /home/distelli/distelli -manager sysv -manager-dir /home/distelli/sysv
   ~~~

1. Copy the init scripts

	Finally you should look in the /home/distelli/sysv directory for the server init scripts. These scripts need to be moved to their appropriate place so that when the server reboots, the Pipelines agent is restarted.

## Run the agent as a different user

If you need to run the Pipelines agent as a different user without using sudo, set up a new user account and install the agent for that user. 

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
   
1. Install the Pipelines agent using either Wget or curl:

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



