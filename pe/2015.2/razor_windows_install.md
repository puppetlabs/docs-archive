---
layout: default
title: " PE 2015.2 » Razor » Setting Up and Installing Windows on Nodes"
subtitle: "Set Up Windows Installations"
canonical: "/pe/latest/razor_windows_install.html"
---

In order to provision machines with Windows, you must first create the Windows install. For licensing reasons, you need to have your own copies of Windows available: both the installer content, and the [Windows Assessment and Deployment](http://msdn.microsoft.com/en-us/library/windows/hardware/hh825486.aspx) toolkit, containing the Windows Preinstallation Environment (WinPE) that's used to automate the Windows installer.

The included stock Windows tasks, which correspond to Windows versions, are as follows:

 * Windows 8.0 Professional
 * Windows 2012 R2
 * Windows 2008 R2 (**Note**: This version requires Windows Management Framework 4.0)

 **Note**: You can also [create your own Windows tasks](./razor_objects.html#writing-task-templates).

## Setting Up a Windows Installation

Making Windows installable by Razor is a multi-step process. Licensing on WinPE requires that you build your own custom WinPE WIM image containing Razor scripts, because we cannot redistribute a pre-built image.  The first stage is to build your own WinPE image suitable for use with Razor, as described below.

1. Install the [Windows Assessment and Deployment](http://msdn.microsoft.com/en-us/library/windows/hardware/hh825486.aspx) in the default location.
2. Copy the `build-winpe` directory content to a Windows machine. If it's absent from your Razor server, use [this archive](http://links.puppetlabs.com/razor-build-winpe).
3. Change into that directory, for example, `c:\build`.
4. Run this build script: `powershell -executionpolicy bypass -noninteractive -file build-razor-winpe.ps1 -razorurl http://razor:8150/svc`

It takes a while for the build script to run. Eventually, an image will be output that matches `*.wim` under the current working directory.  This is your custom WinPE image with the required components to work with the Razor server.

### Create a Repo

Ordinarily, you would create a repository with the command `razor
create-repo --name=<repo name> --iso-url <URL> --task <task
name>`. Unfortunately, Windows DVD images can generally not be unpacked by
Razor because of a limitation of the `libarchive` library that Razor uses
for that purpose.

As a workaround, create a stub repository and fill it manually
with content.

1. Copy the ISO onto the Razor server.
2. Run `razor tasks` to see the available tasks on the server. For the following steps, we will use `$task_name` for the name of the task.
3. Run the following command with the Razor
client. The repo name can be whatever you want. In the following example, which creates a Windows 2008 R2 repo, we match the repo name and the task name:

		razor create-repo --name win2012r2 --task windows/2012r2
           --no-content true

2. Once this command completes successfully, log into your Razor server as
root and cd into your server's `repo_store_root` set in `config.yaml`. Then
run:


        $ mount -o loop /path/to/windows_server_2012_r2.iso /mnt
        $ cp -pr /mnt/* win2012r2
        $ umount /mnt
        $ chown -R pe-razor: win2012r2


### Add the WinPE image to your repo

Copy the `razor-winpe*.wim` image that you built onto your Razor server
and place it into the repository directory created in the previous step as
the file `razor-winpe.wim`.

In the previous example, you would simply run this command:

      # cp /some/where/*.wim win2012r2/razor-winpe.wim

### Create SMB share

Because neither the WinPE environment nor the Windows installer can use an HTTP source for installation, you must configure a server message block (SMB) server to store the Razor repositories.

1. On the Razor server, install Samba: `yum install samba`.
2. Navigate to the Samba directory: `cd /etc/samba`.
3. Edit the `smb.conf` file:
   * Modify the network settings as necessary for your environment.
   * Edit the `global` service definition to allow unauthenticated access:

     ~~~
     [global]
         security     = user
         map to guest = bad user
     ~~~

   * Add a service definition for Razor that allows anonymous access and points to the repo store root specified in your config.yaml file, for example:

     ~~~
     [razor]
         comment   = Windows Installers
         path      = /opt/puppetlabs/server/data/razor-server/repo
         guest ok  = yes
         writable  = no
         browsable = yes
     ~~~

4. Restart Samba: `service smb restart`.


### Create Razor Policies

Finally, [create your policies](./razor_using.html#step-5-create-a-policy-and-provision-your-node) as normal.

# Using Your Windows Installation

Once you have policies set up, your Windows installation should just work if your policy binds a node.

* * *


[Next: Razor Command Reference](./razor_reference.html)
