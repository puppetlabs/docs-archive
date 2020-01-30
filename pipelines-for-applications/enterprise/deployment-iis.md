---
layout: default
title: "IIS .net application: Deploy on Windows"
---
<br>
In this tutorial I will explore deploying a .NET application to a Windows IIS Server using Pipelines.

Before you begin the tutorial you must:

* Install the Pipelines CLI on the development system. [Installing the CLI](./cli.html)
* Ensure git is installed on the development system. [Download Git](http://git-scm.com/downloads)
* Have a destination server available to deploy software to. 
* Install the Pipelines agent on the destination server. [Installing the agent](./agent.html)

This tutorial assumes you have experience working with:

* [Pipelines CLI](./cli.html)
* [Pipelines Agent](./agent.html) and doing deploys with Pipelines. 

This tutorial will use the BlogEngine.NET code available [here](http://blogengine.codeplex.com/).

> **Note:** This has been tested with Windows Server 2012.

<h3><a name="preparing-the-application"></a>Preparing the Application</h3>

The following instructions are all executed from the same command (cmd) shell prompt.

1. To get started create a directory for building your application.

	~~~
	$ mkdir WinIISBlog
	~~~

1. Change to the directory just created.

	~~~
	$ cd WinIISBlog
	~~~

1. Download the BlogEngine.NET (Web) code into this new directory. You can get the code [here](http://blogengine.codeplex.com/downloads/get/910931).

    Now you will need to create the deploy instructions for Pipelines. This is done with the distelli-manifest.yml file.

1. In the WinIISBlog directory create the file `distelli-manifest.yml`.
1. Copy and Paste the following into the `distelli-manifest.yml` file.

~~~
&lt;username&gt;/WinIISBlog:
  PkgInclude:
    - '*.zip'
  Env:
    # Define the TCP port you want the IIS application to listen on
    - PORT: enter_value_here
    # Define the Name for the IIS application
    - SITE: enter_sitename_here
    # Define the Directory full path where the IIS application should reside
    - APPDIR: enter_directory_here
  PreInstall:
    - echo "Begin PreInstall"
    - echo "Install any IIS Prerequisites"
    - echo "---"
    - echo "Removing existing IIS Site"
    - echo "---"
    - echo "Create Application Directory"
    - echo "---"
    - echo "Creating IIS Site"
    - echo "---"
    - echo "Done PreInstall"
  PostInstall:
    - echo "Begin PostInstall"
    - echo "Unzip BlogEngine.NET to Application Directory"
    - echo "---"
    - echo "Set Directory Attributes"
    - echo "---"
    - echo "Done PostInstall"
  Start:
    - echo "Begin Start"
    - echo "Start IIS Site"
    - echo "---"
    - echo "Open Firewall"
    - echo "---"
    - echo "Done Start"
~~~

The above is a skeleton manifest file. The following will discuss what needs to be added to the various manifest sections to successfully deploy this IIS application.

<h3><a name="username"></a>Username</h3>

First step is to change the <b>&lt;username&gt;</b> field to your Pipelines user name. If you need assistance in finding your user name see [Finding Your Pipelines Username](./account.html).
For example if your user name is <b>johndoe</b> your manifest first line would look like this:

~~~
johndoe/WinIISBlog:
~~~


<h3><a name="pkginclude-section"></a>PkgInclude Section</h3>

This section defines which files make up your application that need to be deployed to the destination Windows Server. In this scenario we need only the BlogEngine.zip file. This *.zip syntax will keep all .zip files which will work.

~~~
  PkgInclude:
    - '*.zip'
~~~

<h3><a name="env-section"></a>Env Section</h3>

In this section, you set environment variables that will be used in the balance of the manifest file.

~~~
  Env:
    # Define the TCP port you want the IIS application to listen on
    - PORT: enter_value_here
    # Define the Name for the IIS application
    - SITE: enter_sitename_here
    # Define the Directory full path where the IIS application should reside
    - APPDIR: enter_directory_here
~~~

Change the values as appropriate for your environment. Here are some notes and suggestions:

<h4>Port: "8085"</h4>

Enter a TCP Port number to listen to for this application. Don't use an existing port that is being listened to. This parameter will be used when adding the Site to IIS and setting up firewall rules.

<h4>SITE: BlogEngine</h4>

Enter a name for the IIS Site. This parameter will be used when adding the Site to IIS.

<h4>APPDIR: c:\blog</h4>

Enter the full path to the directory where the application will be served from. This directory should not already exist. This is the directory where the BlogEngine application will be unzipped to and run from.
Here is the text with suggestions so you can copy/paste:

~~~
  Env:
    # Define the TCP port you want the IIS application to listen on
    - PORT: "8085"
    # Define the Name for the IIS application
    - SITE: BlogEngine
    # Define the Directory full path where the IIS application should reside
    - APPDIR: c:\blog
~~~

<h3><a name="preinstall---install-any-iis-prerequisites"></a>PreInstall - Install any IIS Prerequisites</h3>

In my scenario, the Windows 2012 server did not have IIS, ASP, and .NET provisioned, so I chose to install these dependencies on deploy, which I was able to accomplish with the following commands:

~~~
dism /enable-feature /online /featurename:IIS-WebServer /all
dism /enable-feature /online /featureName:IIS-ASP /all
dism /enable-feature /online /featureName:IIS-ASPNET /all
dism /enable-feature /online /featureName:IIS-ASPNET45 /all
~~~

My manifest PreInstall "Install any IIS Prerequisites" section looks like this:

~~~
  PreInstall:
    - echo "Begin PreInstall"
    - echo "Install any IIS Prerequisites"
    - dism /enable-feature /online /featurename:IIS-WebServer /all
    - dism /enable-feature /online /featureName:IIS-ASP /all
    - dism /enable-feature /online /featureName:IIS-ASPNET /all
    - dism /enable-feature /online /featureName:IIS-ASPNET45 /all
    - echo "---"
~~~


> **Note:** Your system may require installation of other prerequisites.

<h3><a name="preinstall---removing-existing-iis-site"></a>PreInstall - Removing Existing IIS Site</h3>

The syntax to create a site with IIS is:

~~~
%windir%\system32\inetsrv\appcmd add site
~~~

The above command will fail and set %ERRORLEVEL% if the site already exists, which will cause the deploy to fail, so providing some code to check for an existing site and remove it first will be helpful.

~~~
    - echo "Removing existing IIS Site"
    - for /F "tokens=*" %%a in ('%windir%\system32\inetsrv\appcmd list site %SITE%') do set RESPONSE=%%a
    - if "%RESPONSE%"=="" (
    -   echo "%SITE% doesn't exist"
    - ) else (
    -   echo "%SITE% does exist. Deleting"
    -   (%windir%\system32\inetsrv\appcmd delete site %SITE%)
    - )
    - echo "---"
~~~

This code will check IIS to see if %SITE% exists. (Remember %SITE% is the environment variable you set in the Env: section above.) If it does exist, it will be deleted.

<h3><a name="preinstall---create-application-directory"></a>PreInstall - Create Application Directory</h3>

In this section you will need to create the application directory. Of note, if you try to do a "mkdir" for a directory that already exists, mkdir will set %ERRORLEVEL% which will cause the Pipelines deploy to fail. You can use "IF not exist" syntax to check for the directory before creating.
Realize that if the directory is not empty, you may encounter unknown issues.

<h4>Approach One</h4>

~~~
    - echo "Create Application Directory"
    - IF not exist %APPDIR% (mkdir %APPDIR%)
    - echo "---"
~~~

Another approach may be to "quietly" remove any existing directory and all its contents before recreating. Note, "rmdir" does not set %ERRORLEVEL% if the directory doesn't exist so it's safe.
<h4>Approach Two</h4>

~~~
    - echo "Create Application Directory"
    - rmdir /S/Q %APPDIR%
    - mkdir %APPDIR%
    - echo "---"
~~~

Realize the above depicts two different approaches and they shouldn't be used together. I prefer the latter as it ensures the directory is empty.

<h3><a name="preinstall---creating-iis-site"></a>PreInstall - Creating IIS Site</h3>

Ultimately , after ensuring the site doesn't exist and the application directory is empty, the IIS site will need to be created with the following syntax:

~~~
    - echo "Creating site %SITE%"
    - (%windir%\system32\inetsrv\appcmd add site /name:%SITE% /physicalPath:%APPDIR% /bindings:http://*:%PORT%)
    - echo "---"
~~~

This ends the PreInstall section of the manifest.

<h3><a name="postinstall---unzip-blogenbing.net-to-application-directory"></a>PostInstall - Unzip BlogEnbing.NET to Application Directory</h3>

Between the PreInstall and PostInstall phases, the Pipelines deploy does the "Install" phase where the application release bundle is downloaded to the destination Windows Server. This means that the BlogEngine.zip file is available, on the disk, in the PostInstall section. This file can be found in the %DISTELLI_INSTALLHOME% directory. For more information on System Environment variables see [Environment Variables Reference Guide](./environment-variable.html).
You can use powershell to unzip the contents of the BlogEngine.zip file with one of the following syntax:
<h4>For Powershell ver 2</h4>

~~~
PowerShell (New-Object -COM Shell.Application).NameSpace('"%APPDIR%"').CopyHere((New-Object -COM Shell.Application).NameSpace('"%DISTELLI_INSTALLHOME%\ZIPFILE.zip"').Items(), 16);
~~~

<h4>For Powershell ver 3</h4>

~~~
powershell add-type -assemblyname "system.io.compression.filesystem" ; [System.io.compression.zipfile]::ExtractToDirectory('"%DISTELLI_INSTALLHOME%\ZIPFILE.zip"','"%APPDIR%"')
~~~

For this section the manifest syntax is:

~~~
  PostInstall:
    - echo "Begin PostInstall"
    - echo "Unzip BlogEngine.NET to Application Directory"
    # Using Powershell version 2
    #- PowerShell (New-Object -COM Shell.Application).NameSpace('"%APPDIR%"').CopyHere((New-Object -COM Shell.Application).NameSpace('"%DIStELLI_INSTALLHOME%\BlogEngine.NET 3.1 (web).zip"').Items(), 1564);
    # Using Powershell version 3+
    - powershell add-type -assemblyname "system.io.compression.filesystem" ; [System.io.compression.zipfile]::ExtractToDirectory('"%DIStELLI_INSTALLHOME%\BlogEngine.NET 3.1 (web).zip"','"%APPDIR%"')
    - echo "---"
~~~

Unremark the syntax that is appropriate for your version of Powershell on your destination Windows Server.

<h3><a name="postinstall---set-directory-attributes"></a>PostInstall - Set Directory Attributes</h3>

According to the install instructions for BlogEngine.NET, found [here](http://blogengine.codeplex.com/wikipage#FreshInstallation); the App_Data directory must have permissions set for the IIS User to read and write data. This can be accomplished in the PostInstall section after the files are unzipped to the disk.

~~~
    - echo "Set Directory Attributes"
    - attrib -r %APPDIR%\App_Data /s /d
    - icacls "%APPDIR%\App_Data" /grant IIS_IUSRS:(OI)(CI)F /t
    - icacls "%APPDIR%\App_Data" /grant IUSR:(OI)(CI)F /t
    - echo "---"
~~~

And this completes the PostInstall section.

<h3><a name="start---start-iis-site"></a>Start - Start IIS Site</h3>

This section will ensure the IIS Site %SITE% is started using the following syntax:

~~~
  Start:
    - echo "Start IIS Site"
    - (%windir%\system32\inetsrv\appcmd start site %SITE%)
    - echo "---"
~~~


<h3><a name="start---open-firewall"></a>Start - Open Firewall</h3>

By default, the Windows firewall is enabled and will block traffic destined to the newly added IIS Site. You can setup rules to allow access to the IIS Site based on the TCP Port number %PORT%.
I found that you can redundantly add the same firewall rule multiple times, so to keep things clean, and avoid a deploy failure, I wanted to ensure rules were being deleted before adding. Before deleting I actually add the rule to avoid getting an error.

~~~
    - echo "Open Firewall"
    - set FWNAME="%SITE% fw rule TCP Port %PORT%"
    - echo "Adding %FWNAME%"
    - netsh advfirewall firewall add rule name=%FWNAME% dir=in action=allow protocol=TCP localport=%PORT%
    - netsh advfirewall firewall delete rule name=%FWNAME%
    - netsh advfirewall firewall add rule name=%FWNAME% dir=in action=allow protocol=TCP localport=%PORT%
    - netsh advfirewall firewall add rule name=%FWNAME% dir=out action=allow protocol=TCP localport=%PORT%
    - echo "---"
~~~

That is it for the manifest. Save your file changes and get ready to deploy this application to your Windows Server.

<h3><a name="steps-to-deploy-this-application"></a>Steps to Deploy this Application</h3>

<ol>
<li>Load the Pipelines Agent on your destination Windows Server where you are deploying the application to. The Pipelines Agent will connect your Server to your Pipelines Account so you can deploy applications to the server.</li>
<li>Pipelines create - In the WinIISBlog directory, where the distelli-manifest.yml file you just created exists, run the <a href="./cli-command.html">distelli create></a> command. This assume you have the <a href="./cli.html">Pipelines CLI</a> Installed on this system. </li>

Remember to replace &lt;username&gt; with your Pipelines username.

<code>distelli create &lt;username&gt;/WinIISBlog</code>

<p>This will create the application in your Pipelines Account.</p>

<li>Pipelines push - Push the first release of the WinIISBlog application up to your Pipelines Account.</li>

<code>distelli push -m \First Release\</code>

<li>With your web browser log into the Pipelines web UI at <a href="https://pipelines.puppet.com/login">pipelines.puppet.com/login</a>.</li>
<li>Find and click the Application name <b>WinIISBlog</b>.</li>
<li>Create an Application Environment.</li>
<ol>
<li>Click <b>Environments</b> link.</li>
<li>Click <b>Create Environment</b> link.</li>
<li>Enter the "Name" <b>WinIISBlog_Env</b> and click <b>Create Environment</b>.</li>
</ol>
<li>Add your server to the Application Environment</li>
<ol>
<li>From the WinIISBlog_Env page, which you should be on, click <b>Servers</b> link.</li>
<li>Click the <b>Add Servers</b> link.</li>
<li>Find your Windows Server, you installed the agent on earlier, and check the <b>Add Server</b> box.</li>
<li>Click the <b>Add Selected Servers</b> link.</li>
</ol>
<li>Deploy your First Release to the Environment</li>
<li>Click <b>Details</b> link.</li>
<li>In the <b>Release</b> list click the <b>Deploy</b> button for your "First Release".</li>
<li>In the "New Deployment" dialog click the <b>Deploy</b> button.</li>
</ol>

The WinIISBlog application is now being deployed to your Windows Server in the WinIISBlog_Env environment. You can click the <b>View Log</b> button to watch the logging of the deployment phases.
Note: The PreInstall of the IIS requirements can take a "more than expected" time to complete.
If you have a failure deploying, as a suggestion, you can add more debugging by adding more `echo` statements to the distelli-manifest.yml. Remember that when you make changes you have to do a `distelli push -m "Message"` to update the Release in your Pipelines account.

<h3><a name="the-complete-manifest"></a>The Complete Manifest</h3>

~~~
&lt;username&gt;/WinIISBlog:
  PkgInclude:
    - '*.zip'
  Env:
    # Define the TCP port you want the IIS application to listen on
    - PORT: "8085"
    # Define the Name for the IIS application
    - SITE: BlogEngine
    # Define the Directory full path where the IIS application should reside
    - APPDIR: c:\blog
  PreInstall:
    - echo "Begin PreInstall"
    - echo "Install any IIS Prerequisites"
    - dism /enable-feature /online /featurename:IIS-WebServer /all
    - dism /enable-feature /online /featureName:IIS-ASP /all
    - dism /enable-feature /online /featureName:IIS-ASPNET /all
    - dism /enable-feature /online /featureName:IIS-ASPNET45 /all
    - echo "---"
    - echo "Removing existing IIS Site"
    - for /F "tokens=*" %%a in ('%windir%\system32\inetsrv\appcmd list site %SITE%') do set RESPONSE=%%a
    - if "%RESPONSE%"=="" (
    -   echo "%SITE% doesn't exist"
    - ) else (
    -   echo "%SITE% does exist. Deleting"
    -   (%windir%\system32\inetsrv\appcmd delete site %SITE%)
    - )
    - echo "Create Application Directory"
    - rmdir /S/Q %APPDIR%
    - mkdir %APPDIR%
    - echo "---"
    - echo "Creating IIS Site"
    - (%windir%\system32\inetsrv\appcmd add site /name:%SITE% /physicalPath:%APPDIR% /bindings:http://*:%PORT%)
    - echo "---"
    - echo "Done PreInstall"
  PostInstall:
    - echo "Begin PostInstall"
    - echo "Unzip BlogEngine.NET to Application Directory"
    # Using Powershell version 2
    #- PowerShell (New-Object -COM Shell.Application).NameSpace('"%APPDIR%"').CopyHere((New-Object -COM Shell.Application).NameSpace('"%DIStELLI_INSTALLHOME%\BlogEngine.NET 3.1 (web).zip"').Items(), 1564);
    # Using Powershell version 3+
    - powershell add-type -assemblyname "system.io.compression.filesystem" ; [System.io.compression.zipfile]::ExtractToDirectory('"%DIStELLI_INSTALLHOME%\BlogEngine.NET 3.1 (web).zip"','"%APPDIR%"')
    - echo "---"
    - echo "Set Directory Attributes"
    - attrib -r %APPDIR%\App_Data /s /d
    - icacls "%APPDIR%\App_Data" /grant IIS_IUSRS:(OI)(CI)F /t
    - icacls "%APPDIR%\App_Data" /grant IUSR:(OI)(CI)F /t
    - echo "---"
    - echo "Done PostInstall"
  Start:
    - echo "Begin Start"
    - echo "Start IIS Site"
    - (%windir%\system32\inetsrv\appcmd start site %SITE%)
    - echo "---"
    - echo "Open Firewall"
    - set FWNAME="%SITE% fw rule TCP Port %PORT%"
    - echo "Adding %FWNAME%"
    - netsh advfirewall firewall add rule name=%FWNAME% dir=in action=allow protocol=TCP localport=%PORT%
    - netsh advfirewall firewall delete rule name=%FWNAME%
    - netsh advfirewall firewall add rule name=%FWNAME% dir=in action=allow protocol=TCP localport=%PORT%
    - netsh advfirewall firewall add rule name=%FWNAME% dir=out action=allow protocol=TCP localport=%PORT%
    - echo "---"
    - echo "Done Start"
~~~

