---
layout: default
title: "Wordpress Web App and MySQL on Docker Compose"
---
<style>
img.ttImages{
    display:block;
    margin-left:auto;
    margin-right:auto;
    box-shadow:-6px -6px 8px #888888, 6px -6px 8px #888888;
}</style>
<br>

Docker Compose is a tool for defining and running multi-container Docker applications. This tutorial will walk you through how to deploy a Wordpress application using a MySQL Database. 

## Before You Begin

To successfully complete this tutorial you will need:

* [A free Pipelines account](https://pipelines.puppet.com/signup)
* A [GitHub](https://www.github.com) or [BitBucket](https://bitbucket.org/) Account
* A server with <a href="https://docs.docker.com/engine/quickstart/" target="_blank">Docker</a> and <a href="https://docs.docker.com/compose/install/" target="_blank">Docker Compose</a> Installed

## Step 1. Create Docker Compose Application

The first task we must accomplish is creating our Wordpress and MySQL application and our configuration files for Docker Compose and Pipelines. To start create a directory to store you files, I am calling mine `compose_wordpress`.

In your directory create a `Dockerfile` with the contents below:

#### Dockerfile

~~~
FROM wordpress:4.5.0
ADD . /code
~~~

Next we need to create `docker-compose.yml` with the following contents:

#### docker-compose.yml

~~~
version: '2'
services:
  web:
    build: .
    command: php -S 0.0.0.0:8000 -t /code/wordpress/
    ports:
      - "80:8000"
    depends_on:
      - db
    volumes:
      - .:/code
  db:
    image: mysql
    environment:
      MYSQL_ALLOW_EMPTY_PASSWORD: yes
      MYSQL_DATABASE: wordpress
~~~

Now we need to download Wordpress into our project's directory. To do this, run the following command:

~~~
$ curl https://wordpress.org/latest.tar.gz | tar -xvzf -
~~~

To finish setting up our Wordpress application you will need to create `wp-config.php` in the `wordpress/` directory. This file will point our application to our database.

#### wp-config.php

~~~
&#60;?php
define('DB_NAME', 'wordpress');
define('DB_USER', 'root');
define('DB_PASSWORD', '');
define('DB_HOST', "db:3306");
define('DB_CHARSET', 'utf8');
define('DB_COLLATE', '');

define('AUTH_KEY',         'put your unique phrase here');
define('SECURE_AUTH_KEY',  'put your unique phrase here');
define('LOGGED_IN_KEY',    'put your unique phrase here');
define('NONCE_KEY',        'put your unique phrase here');
define('AUTH_SALT',        'put your unique phrase here');
define('SECURE_AUTH_SALT', 'put your unique phrase here');
define('LOGGED_IN_SALT',   'put your unique phrase here');
define('NONCE_SALT',       'put your unique phrase here');

$table_prefix  = 'wp_';
define('WPLANG', '');
define('WP_DEBUG', false);

if ( !defined('ABSPATH') )
  define('ABSPATH', dirname(__FILE__) . '/');

require_once(ABSPATH . 'wp-settings.php');
?&#62;
~~~

The last file we will create is the `distelli-manifest.yml`. The manifest is a set of instructions for building and serving our application.

#### distelli-manifest.yml

> **Note:** Replace <-username-> with your Pipelines username & <-app name-> with your Pipelines Application's Name. 

~~~
<-username->/<-app name->:
	Build:
	  - echo "Nothing to build"
	PkgInclude:
	  - '*'
	Env:
	  # Set Env variables here
	  - PORT: "8000"
	Exec:
	  - cd $DISTELLI_INSTALLHOME
	  - sudo /usr/local/bin/docker-compose up
	PostStart:
	  - publicip=$(curl -s ident.me) || true
	  - 'echo "You can validate the install by pointing your browser at http://$publicip"'
~~~

That's it! We are ready to deploy our application. To be sure, you can double check the your project structure with the structure below. 

<img class="ttImages" src="images/NodeTutorial/WordpressCompose/projectStructure.png" alt="Docker Compose Project Structure" />

## Step 2. Push your Code to GitHub/BitBucket

The next step to complete is to put your code into a source control repository. If you are unfamiliar with source control the steps below will guide through the process. For this tutorial I am using GitHub as my repository, but you can use BitBucket as well without any issue.

First you will need to create a repo in GitHub and push your code to that repository. Once created, open your terminal and make sure you are in the directory that your app's code is in.

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

Navigate to your GitHub or BitBucket Repo and find your remote repository URL. Once you have your remote repository URL use the following commands to finish setting up your repo.

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

## Step 3. Create Pipelines Application

If you do not have a Pipelines account, please [Sign Up](http://pipelines.puppet.com/signup) for one now. 

To create your app in Pipelines, follow these steps:

Click the new application button on your Pipelines account home screen

<img class="ttImages" src="images/NodeTutorial/distelliCreateNewApp.png" alt="Create a new Pipelines Application" />

Select the Repository type where you are storing your applications files. 

<img class="ttImages" src="images/NodeTutorial/distelliCreateNewAppBlank.png" alt="Name Your Pipelines App" />

After we click the button to connect to our Repository, we then select the appropriate repo that contains your code.

<img class="ttImages" src="images/NodeTutorial/WordpressCompose/wpDockerSelectRepo.png" alt="Choose Your Pipelines Repo" />

Select the appropriate branch for your deployment. I have only a master branch, but you can deploy any branch from your repo!

<img class="ttImages" src="images/NodeTutorial/distelliChooseBranch.png" alt="Choose Your Pipelines Repo's Branch" />

Next Pipelines will ask you set your build steps. We have created a `distelli-manifest.yml`, so we will check the checkbox to use this file:

<img class="ttImages" src="images/NodeTutorial/goLang/goLangUseManifest.png" alt="Wordpress Docker Compose Use Manifest" />

The final step is select our Build Image. In this case we are going to select "Distelli Base" for our Application. Select the <b>Auto Build</b> checkbox and the the <b>Looks good. Start Build!</b>.

> **Note:** The Auto-Build feature builds your application every time you commit code to your repository

<img class="ttImages" src="images/NodeTutorial/WordpressCompose/wpDockerBuildStart.png" alt="Start Pipelines Build" />

Visit the <b>Builds</b> to view the progress of your application's build.

<img class="ttImages" src="images/NodeTutorial/WordpressCompose/wpDockerBuildSuccess.png" alt="Succesful Wordpress Docker App Build" />

## Step 4. Install Pipelines Agent and Add Server

The next step in deploying our app is to make sure you have a server to run your application on. You can use any type of server - public cloud, private cloud or physical. Pipelines will let you deploy your code to  Vagrant VMs or servers in a VPC. You can even deploy to the server under your desk. For this tutorial I am going to be deploying to a server hosted by DigitalOcean. 

> **Note:** You will need remote access to the server you are deploying to & root (administrator) permissions.

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

### Create New Environment

Now that we have installed the Pipelines Agent we need to create an environment and add our server to that environment. To start navigate to your application's page in Pipelines and click on the <b>Environments</b> tab.

<img class="ttImages" src="images/NodeTutorial/WordpressCompose/wpComposeEnvironmentTab.png" alt="Pipelines Environment Tab" />

On the Environments page click the <b>New Environment</b> button. Enter in a name for your new environment and then click the <b>Create Environment</b> button.

<img class="ttImages" src="images/NodeTutorial/Bugzilla/bugzillaNewEnvironment.png" alt="Create new Pipelines Environment" />

You will then be directed to your new Environment's page in Pipelines. This is where we are going to set our environment variables. Click on the environment variable section

<img class="ttImages" src="images/NodeTutorial/Bugzilla/bugzillaEnvironmentVariables.png" alt="Pipelines environment variables" />

Enter in the following variables:

> **Note:** Make sure to fill out the variables with your information. Do not leave them blank.

~~~
USER=""
BUG_PW=""
MYSQL_ROOT=""
ADMIN_EMAIL=""
ADMIN_PASSWORD=""
ADMIN_REALNAME=""
NO_PAUSE="1"
~~~

Once you have defined your variables click the <b>Set Variables</b> button. Now we are ready to deploy our application. Navigate back to the <b>Builds</b> page to start deploying your application.

## Step 5. Deploy Application

Now that we have successfully built our application we are ready to deploy to our server. On the builds page select the <b>New Deployment</b> button in the right hand corner.

<img class="ttImages" src="images/NodeTutorial/deployNewDeployment.png" alt="Go Pipelines Build" />

### Deploy your Application

Once you click the button, you should be directed towards Pipelines's deployment page. The first step is to select the <b>Deploy a Release</b> option.

<img class="ttImages" src="images/NodeTutorial/distelliDeployRelease.png" alt="Deploy Pipelines Release" />

Pipelines will now prompt you to select the application you want to deploy. Please select the application we created earlier in the tutorial.

<img class="ttImages" src="images/NodeTutorial/WordpressCompose/wpDockerSelectApp.png" alt="Select Pipelines Application for Deployment" />

Then you will be asked to select the release you would like to deploy. Make sure to select your latest release, which is located at the top of the list.

<img class="ttImages" src="images/NodeTutorial/WordpressCompose/wpDockerSelectRelease.png" alt="Choose Your Pipelines Release" />

The last step in our deployment is to select the environment you wish to deploy in. First you will need to create an environment by enter a name and selecting the <b>Add Environment</b> button.

<img class="ttImages" src="images/NodeTutorial/deployCreateEnvironment.png" alt="Add Pipelines Deployment Environment" />

Next select the Environment you just created and click <b>All Done</b>.

<img class="ttImages" src="images/NodeTutorial/deploySelectEnvironment.png" alt="Select Pipelines Deployment Environment" />

Now Pipelines will ask you to add your server. Click the <b>Add Servers</b> button to get started. Select the server you configured earlier in the tutorial and add it to your account. Once you have added your server, you can close the <b>Add Servers</b> panel and continue with your deployment. You will see a final option to set your delay between deployments on your servers and a <b>Deploy!</b> button.

<img class="ttImages" src="images/NodeTutorial/WordpressCompose/wpDockerStartDeployment.png" alt="Start Your Pipelines Deployment" />

Click the <b>Deploy!</b> button to begin your deployment. You will redirected to a page where you can view the progress of your deployment. Click the <b>log</b> button on the left side to view real time streaming logs for the deployment.

<img class="ttImages" src="images/NodeTutorial/WordpressCompose/wpDockerDeploySuccess.png" alt="Pipelines Deployment Successful" />

### View Your Application

And that's it! Your Wordpress application has been deployed. Point your browser towards <b>http://<-Your Server’s IP Address-></b> and follow the prompt provided by Wordpress to finish setting up your site.

<img class="ttImages" src="images/NodeTutorial/WordpressCompose/wpDockerAppDeployed.png" alt="Wordpress Successful Deployment" />

<img class="ttImages" src="images/NodeTutorial/WordpressCompose/wpDockerSiteDash.png" alt="Wordpress Dashboard" />



