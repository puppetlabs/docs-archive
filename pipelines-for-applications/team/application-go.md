---
layout: default
title: "Go application: Build and deploy"
---
<style>
img.ttImages{
    display:block;
    margin-left:auto;
    margin-right:auto;
    box-shadow:-6px -6px 8px #888888, 6px -6px 8px #888888;
}</style>
<br>

This tutorial will get you setup and deploying a Go application with Pipelines.
The tutorial assumes you have already:

* [Created a Pipelines account](https://pipelines.puppet.com/signup)
* Have a [GitHub](https://www.github.com) or [BitBucket](https://bitbucket.org/) Account
* A basic knowledge of Go

It is also important to note that I am building and deploying on Linux Ubuntu servers.

### Step 1. Create Application

For this tutorial we will be deploying a Go web-app. Here is the structure of the project we will be deploying:

<img class="ttImages" src="images/NodeTutorial/goLang/goLangProjectStructure.png" alt="Go Language App Project Structure" />

For this tutorial we will be using a basic "Hello World" Go web-application, but you can use your own custom application if you like. Below are the contents of my web application. My file is named `server.go`.

#### server.go

~~~
package main

import (
  "os"
    "fmt"
    "net/http"
)

func handler(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("content-type", "text/html")
    fmt.Fprintf(w, "&#60;h2&#62;Hello World from Distelli! You have a working Go application Deployment!&#60;/h2&#62;")
}

func main() {
  port := os.Args[1]
  fmt.Println(port)

    http.HandleFunc("/", handler)
    http.ListenAndServe(":"+port, nil)
}

~~~

Next we will create our `distelli-manifest.yml` file. The manifest is a set of instructions that Pipelines uses to build and deploy your application:

#### `distelli-manifest.yml`

> **Note:** Replace <-username-> with your Pipelines username and <-app name-> with your Pipeline application name.

~~~
<-username->/<-app name->:
  # Pipeline Manifest example
  # this example assumes the build and deploy servers are:
  # The same architecture (32 vs 64 bit)
  # Ubuntu 14.04
  # Installed Packages: apt-get
  #

  PreRelease:
    - echo "–--Beginning PreRelease---"
    - echo "--Installing build dependencies--"
    - echo "-Updating apt-get-"
    - sudo apt-get -y update
    - echo "-Installing GoLang-"
    - sudo apt-get -y install golang
    - echo “--Building--”
    - cd src
    - go build -o ../bin/server
    - echo “--Testing--”
    - go test

  PkgInclude:
    - bin/server

  Env:
    - PORT: "8002"

  Exec:
    - exec ./bin/server $PORT
~~~

For more detailed information on the <b>Pipelines manifest</b>, visit our <a href="./manifest.html" target="_blank">Documentation</a>.

## Step 2. Push your Code to GitHub/BitBucket

The next step to complete is to put your code into a source control repository. If you are unfamiliar with source control the steps below will guide through the process. For this tutorial I am using GitHub as my repository, but you can use BitBucket as well without any issue.

First you will need to create a repo in GitHub and push your code to that repository. When that process is complete, open your terminal and make sure you are in the directory that your app's code is in.

Initialize the local directory as a Git Repo
~~~
$ git init
~~~

Add the files in your new local repo
~~~
$ git add .
~~~

Commit the files you have just staged
~~~
$ git commit -m "First Commit"
~~~

Navigate to your GitHub or BitBucket Repo and find your remote repository URL. Use the following commands to finish setting up your repo.

~~~
$ git remote add origin "Your Remote Repository URL"
# Sets the new URL
$ git remote -v
# Verify the URL
~~~

Now push the changes to your repo
~~~
$ git push origin master
~~~

## Step 3. Set Up Your Server

If you do not have a Pipelines account, please [Sign Up](http://pipelines.puppet.com/signup) for one now.

The next step in deploying our app is to make sure you have a server to run your application on. You can use any type of server - public cloud, private cloud or physical. Pipelines will let you deploy your code to  Vagrant VMs or servers in a VPC. You can even deploy to the server under your desk. For this tutorial I am going to be deploying to a server hosted by DigitalOcean.

**Note:** You need remote access to the server you are deploying to and root (administrator) permissions.

### Install Pipelines Agent on Your Server

To be able to deploy your application to your server you will need to install the Pipelines Agent. You will need remote access to your server to complete the installation. Please consult the below information on how to install the Pipelines Agent on your server.

#### Linux and macOS X

To install on Linux or macOS X you can use either curl <b>or</b> wget with one of the following syntaxes.
##### wget example

~~~
wget -qO- https://pipelines.puppet.com/download/client | sh
~~~

##### curl example

~~~
curl -sSL https://pipelines.puppet.com/download/client | sh
~~~

#### Complete the Install

To complete the install of the agent, you must issue the `/usr/local/bin/distelli agent install` command.

~~~
/usr/local/bin/distelli agent install
~~~

~~~
ServerA:~$ <b>wget -qO- https://pipelines.puppet.com/download/client | sh</b>
This script requires superuser privileges to install packages
Please enter your password at the sudo prompt

[sudo] password for bmcgehee:
    Installing Distelli CLI 3.51 for architecture 'Linux-x86_64'...
    Downloading https://s3.amazonaws.com/download.distelli.com/distelli.Linux-x86_64/distelli.Linux-x86_64-3.51.gz
To install the agent, run:
    sudo /usr/local/bin/distelli agent install
ServerA:~$ <b>sudo /usr/local/bin/distelli agent install</b>
Distelli Email: jdoe@distelli.com
      Password:
    1: User: jdoe
    2: Team: janedoe/TeamJane
Team [2]: <b>1</b>
Server Info: https://www.distelli.com/jdoe/servers/12345678-4765-ac42-bd7a-080027c8277c
Starting upstart daemon with name:
~~~

#### Verify the Install

To validate the agent is installed and working use the `/usr/local/bin/distelli agent status` command.

> **Note:** This installation requires root (administrator) permissions.

~~~
/usr/local/bin/distelli agent status
Distelli Agent (serverA) is Running with id
~~~

If you would like more information on installing the Pipelines agent, visit [Installing the Pipelines Agent](./agent.html). 

## Step 4. Create Pipelines Application

To create your app in Pipeline, follow these steps:

Click the new application button on your Pipelines account home screen

<img class="ttImages" src="images/NodeTutorial/distelliCreateNewApp.png" alt="Create a new Pipelines Application" />

Select the Repository type where you are storing your applications files.

<img class="ttImages" src="images/NodeTutorial/distelliCreateNewAppBlank.png" alt="Name Your Pipelines App" />

After we click the button to connect to our Repository, we then select the appropriate repo that contains your code.

<img class="ttImages" src="images/NodeTutorial/goLang/goLangSelectRepo.png" alt="Choose Your Pipelines Repo" />

Select the appropriate branch for your deployment. I have only a master branch, but you can deploy any branch from your repo!

<img class="ttImages" src="images/NodeTutorial/distelliChooseBranch.png" alt="Choose Your Pipelines Repo's Branch" />

Next Pipelines will ask you set your build steps. Because we have created a `distelli-manifest.yml` we will check the checkbox to use this file:

<img class="ttImages" src="images/NodeTutorial/goLang/goLangUseManifest.png" alt="Go Language Use Manifest" />

The final step is select our Build Image. In this case we are going to select **Distelli Go (Docker)** for our Go Application. Select the <b>Auto Build</b> checkbox and <b>Looks good. Start Build!</b>.

**Note:** The Auto-Build feature builds your application every time you commit code to your repository

<img class="ttImages" src="images/NodeTutorial/goLang/goLangBuildStart.png" alt="Start Pipelines Build" />

Visit the <b>Builds</b> to view the progress of your application's build.

<img class="ttImages" src="images/NodeTutorial/goLang/goLangBuildSuccess.png" alt="Succesful Go Lang App Build" />

## Step 5. Deploy Application

Now that we have successfully built our application we are ready to deploy to our server. On the builds page select the <b>New Deployment</b> button in the right hand corner.

<img class="ttImages" src="images/NodeTutorial/deployNewDeployment.png" alt="Go Pipelines Build" />

### Deploy your Go Application

When you click the button, you should be directed towards the Pipeline deployment page. The first step is to select the <b>Deploy a Release</b> option.

<img class="ttImages" src="images/NodeTutorial/distelliDeployRelease.png" alt="Deploy Pipelines Release" />

Pipelines will now prompt you to select the application you want to deploy. Please select the application we created earlier in the tutorial.

<img class="ttImages" src="images/NodeTutorial/goLang/goLangSelectApp.png" alt="Select Pipelines Application for Deployment" />

Then you will be asked to select the release you would like to deploy. For now there should be only one release for deployment.

<img class="ttImages" src="images/NodeTutorial/goLang/goLangSelectRelease.png" alt="Choose Your Pipelines Release" />

The last step in our deployment is to select the environment you wish to deploy in. First you will need to create an environment by enter a name and selecting the <b>Add Environment</b> button.

<img class="ttImages" src="images/NodeTutorial/deployCreateEnvironment.png" alt="Add Pipelines Deployment Environment" />

Next select the Environment you just created and click <b>All Done</b>.

<img class="ttImages" src="images/NodeTutorial/deploySelectEnvironment.png" alt="Select Pipelines Deployment Environment" />

Now Pipelines will ask you to add your server. Click the <b>Add Servers</b> button to get started. Select the server you configured earlier in the tutorial and add it to your account. Once you have added your server, you can close the <b>Add Servers</b> panel and continue with your deployment. You will see a final option to set your delay between deployments on your servers and a <b>Deploy!</b> button.

<img class="ttImages" src="images/NodeTutorial/goLang/goLangStartDeployment.png" alt="Start Your Pipelines Deployment" />

Click the <b>Deploy!</b> button to begin your deployment. You will be redirected to a page where you can view the progress of your deployment. Click the <b>log</b> button on the left side to view real time streaming logs for the deployment.

<img class="ttImages" src="images/NodeTutorial/goLang/goLangDeploySuccess.png" alt="Pipelines Deployment Successful" />

### View Your Application

And that's it! You should now be able to point your browser to <b>http://<-Your Server's IP Address->:8002</b> and see your Go Application!

<img class="ttImages" src="images/NodeTutorial/goLang/goLangAppDeployed.png" alt="Go Successful Deployment" />


