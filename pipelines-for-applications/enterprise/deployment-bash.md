---
layout: default
title: "Bash application: Deploy"
---

This tutorial will get you setup and deploying a simple Bash application with Pipelines for Applications.

Before you begin, complete the following prerequisites:

* [Create a Pipelines account](https://pipelines.puppet.com/signup).
* [Install the Pipelines CLI](./cli.html) on the development system.
* Ensure Git is installed on the development system. [Download Git](http://git-scm.com/downloads)
* Have a destination server available to deploy software to.
* Install the Pipelines agent on the destination server. [Installing the agent](./agent.html)

The scenario also expects that you have a **Destination** server available to do deployments of the example application.

### 1. Cloning an Application

In this step, you'll clone a sample application from Puppet Pipelines public GitHub repository. These steps should be completed on the Development server where you installed the Pipelines CLI.
Enter the following command to clone the sample Bash application from GitHub:

Clone a sample app repo from GitHub to your local machine:

~~~
git clone https://github.com/Distelli/BashSimpleApp.git
~~~

Then, CD into the sample repo:

~~~
cd BashSimpleApp
~~~

### 2. Inspecting the Manifest File

The Pipelines Manifest file provides the Pipelines Platform with the needed information to successfully deploy your application. This file must exist before pushing your application to Puppet Pipelines.
The sample application you cloned will already have an existing `distelli-manifest.yml` file in the application root directory. You will need to make a small change to this file before you can push the application up to Pipelines.
Edit the `distelli-manifest.yml` file. On the first line you will see a &lt;username&gt; placeholder, and then the name of the sample application.
Replace the &lt;username&gt; placeholder with your Pipelines username. For example if your username is 'YourUserName' your `distelli-manifest.yml` should look like this:

~~~
YourUserName/BashSimpleApp:
  # This Basic Pipelines Manifest has no
  # destination requirements.

  Env:
    - EXAMPLE: '"CHANGE ME TO SOMETHING FUN"'

  Exec:
    - exec ./simplebash.sh

  PkgInclude:
    - simplebash.sh
~~~


**Note:** In .yml (YAML format) files, correct indentation is important.
 

### 3. Creating a Version of the Application on Pipelines

In this step, you'll create a remote version of your application on Pipelines using the **distelli create** command. You need to create the application only one time. After that you can push the application, and subsequent versions, up to Pipelines.
To create an application in Pipelines, enter the following command replacing **YourUserName** with your Pipelines username:

~~~
distelli create YourUserName/BashSimpleApp
~~~


**Note:** The application name must match the application name in the Pipelines Manifest.*,

### 4. Pushing your First Version

In this step, you'll push your first application version to Pipelines using the `distelli push` command.
Enter the following command in the terminal:

~~~
distelli push -m "Initial Release of BashSimpleApp"

Pushing App: BashSimpleApp
Uploading Bundle: [==========] 100%

Creating Release... DONE
~~~

**Note:** Just as it is important to have a succinct comment for software repository checkins, it is best-practice to include a relevant message when pushing or deploying your application up to Pipelines.

### 5. Creating an Environment

When you deploy an application with Pipelines, you can deploy the application to an Environment which may contain one or more servers. You can imagine this in relation to the Software Development Life Cycle where you have several environments which may include; Dev, Test, Staging, and Production. And each of these environments may have 1 or more servers.
In this step, you'll create an environment and add the destination server. To do this:

1. Login to the [Pipelines web UI](https://pipelines.puppet.com/login).
1. Click the **BashSimpleAppp** application name.
1. Click the **Environments** link.
1. Click the **Create Environment** button.
1. Enter the **Name** `Stage-bash`.
1. Click **Create Environment**.

You have just created an environment named Stage-bash.

1. Click the **Servers** link.
1. Click **Add Servers** link.
1. Select the **Add Server** checkbox for the server you installed the agent on.
1. Click the **Add Selected Servers** link.

You have added server(s) to the environment.

### 6. Deploying your Release

In this step, you'll deploy your release to the Stage-bash environment which includes your destination server. On the development server where you installed the Pipelines CLI and the sample application ensure you are in the BashSimpleApp root directory where the Pipelines Manifest file is and use the `distelli deploy` command.

~~~
distelli deploy BashSimpleApp -m "Initial Release" -e Stage-bash
~~~

**Note:** Environment names are case sensitive.

### 7. Validation

During the deployment you will see Deployment In Progress messages and when it is complete you should see:

~~~
Deployment ##### Done: [=         ] Servers: 1 Done, 0 In Progress, 0 Failed
~~~

This application only outputs to stdout, so you will have to look at the stdout log in the Pipelines web UI. To do so, sign in to the [the Pipelines web UI](https://pipelines.puppet.com/login) and navigate to your **BashSimpleApp** application and select the **Deployments** icon.

Here, at the top of the list, you will see your **Deployment #####**. Select it.

On the Deployment page click the **Stdout** button. This Bash script outputs the Server Time every 8 seconds for 10 iterations and then it will exit. When executing an application using the **Exec:** section in the `distelli-manifest.yml` file, if the application stops, Pipelines will automatically relaunch the application. To avoid this behavior, try editing the `distelli-manifest.yml` file and change the section header **Env:** to <b>Start:</b> and watch the **View Log** instead of the Stdout.

### Next Steps

Try adding more servers to the Stage-bash environment and do a deploy to Stage-bash. You will see the deployment will go to all servers in the environment.

