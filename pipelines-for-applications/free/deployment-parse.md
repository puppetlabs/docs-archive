---
layout: default
title: "Parse application: Migrate and deploy"
---
<br>

This guide will help you migrate your Parse application from a Parse hosted back-end to a self-hosted back-end. If you want to use Docker with your Parse-Server, please use this <a href="/docs/tutorials/migrate-parse-app-docker">guide</a>. This is based of <a href="https://github.com/ParsePlatform/parse-server/wiki/Migrating-an-Existing-Parse-App" target="_blank">Parse's migration guide</a>. The guide assumes the following:

* [Created a Pipelines account](https://pipelines.puppet.com/signup)
* Have a Parse Application
* Using MongoDB as your new database
* Beta and Production server ready to be deployed to
* A [GitHub](https://github.com/)/[BitBucket](https://bitbucket.org/) Repository to store your application

## Step 1. Migrate Your Database

Our first step to complete is migrating your Parse database. Parse recommends using MongoDB and they have built a great tool to make the migration as painless as possible. Parse has a Database migration guide [here](https://parse.com/docs/server/guide#database). Before we can continue you will need to have your production database all set up. After you database instance is ready, we can migrate over your Parse data. To start, log in to your Parse account, navigate to your application's settings page, scroll down and click the <b>Migrate to external database</b> option.

<img src="images/Parse/parseStartMigration.png" alt="Parse to MongoDB Migration" />

Once you have successfully migrated your data over to MongoDB we can start the process of migrating the rest of our application.

## Step 2. Create your Parse Server

To get started create a directory on your local machine for your Parse server. In your directory create an `app.js` file and run the following command:

~~~
$ npm init
~~~

Follow the prompts and at the end you will have a `package.json` file. This file will assist you in making the migration from your local machine to your beta/production servers. Next run the following command to install the Parser server module & Express in your project and save it as an application dependency.

~~~
$ npm install parse-server express --save
~~~

Open your `app.js` file and replace the bold text with the values the map back to your application and database:

~~~
var express = require('express');
var ParseServer = require('parse-server').ParseServer;

var app = express();

var port = process.env.PORT || 1337;

// Specify the connection string for your mongodb database
// and the location to your Parse cloud code
var api = new ParseServer({
  databaseURI: <b>YOUR MONGODB CONNECTION STRING</b>,
  cloud: <b>YOUR CLOUD PATH</b>, // Provide an absolute path
  appId: <b>YOUR APP ID</b>,
  masterKey: <b>YOUR MASTER KEY</b>, //Add your master key here. Keep it secret!
  fileKey: <b>OPTIONAL FILE KEY</b>,
  serverURL: 'http://<b>SERVER IP ADDRESS</b>:' + port + '/parse' // Don't forget to change to https if needed
});

// Serve the Parse API on the /parse URL prefix
app.use('/parse', api);

// Hello world
app.get('/', function(req, res) {
  res.status(200).send('Express is running here.');
});

app.listen(port, function() {
  console.log('parse-server-example running on port ' + port + '.');
});

~~~

Save your new `app.js` file. Our next step is to push our Parse-Server application to source control. Commit and push your application to your GitHub or BitBucket Repository.

## Step 3. Create & Set-Up Pipelines Application

Now that we have our Parse-Server application ready to go, we can set up our application in Pipelines. The goal of this step is the create a Beta and Production environment for your new application.

### Create Application

To create your app in Pipelines, follow these simple steps:

Click the new application button on your Pipelines account home screen

<img src="images/NodeTutorial/distelliCreateNewApp.png" alt="Create a new Pipelines Application" />

Select the Repository type where you are storing your applications files.

<img src="images/NodeTutorial/distelliCreateNewAppBlank.png" alt="Name Your Pipelines App" />

After we click the button to connect to our Repository, we then select the appropriate repo that contains your code.

<img src="images/Parse/parseDeploySelectRepo.png" alt="Choose Your Pipelines Repo" />

Select the appropriate branch for your deployment. I have only a master branch, but you can deploy any branch from your repo!

<img src="images/NodeTutorial/distelliChooseBranch.png" alt="Choose Your Pipelines Repo's Branch" />

Next Pipelines will ask you set your build steps. For this particular application we will use the PkgInclude section. The Build section is where you would want to include any tests or tasks you would like to run while your application is building. Please make sure your PkgInclude section looks identical to the one below.

<img src="images/NodeTutorial/deployNodePkgInclude.png" alt="Node.js Distelli PkgInclude" />

The final step is select our Build Image. In this case we are going to select **Distelli Javascript (Docker)** for our Node.js Application. Select the **Auto Build** checkbox and the the **Looks good. Start Build!**

**Note:** The Auto-Build feature builds your application everytime you commit code to your repository

<img src="images/Parse/parseBuildStart.png" alt="Start Pipelines Build" />

Now our application should be automatically building. You can watch its progress by navigating to the builds tab on your Pipelines account and clicking on your build.

### Deploy Steps

**Note:** This tutorial assumes that the server is out of the box and needs to have your application dependencies installed & configured. Once your application dependencies are installed & configured you can remove those commands from your <b>Manifest</b>.

Once your build has completed navigate back to your application page and open your application.

<img src="" alt="" />

Select the Manifest tab

<img src="images/NodeTutorial/distelliManifestTab.png" alt="Manifest Tab for Pipelines App" />

Expand the "Deployment Manifest" section by clicking on the plus sign. This is where we are going to tell Pipelines what commands to run during our Deployment.

### PreInstall

Scroll down to your PreInstall section and enter in the following

<img src="images/NodeTutorial/deployNodeUbuntuPreInstall.png" alt="Distelli Node.js PreInstall Commands" />

Copy/Paste
~~~
echo "Starting PreInstall"
sudo apt-get update
sudo apt-get install nodejs -y
sudo apt-get install npm -y
~~~

### PostInstall

Scroll down to your PostInstall section and enter in the following

<img src="images/NodeTutorial/deployNodeUbuntuPostInstall.png" alt="Distelli Node.js PostInstall Commands" />

Copy/Paste
~~~
echo "Starting PostInstall"
npm install
~~~

### Exec

Scroll down to your Exec section and enter in the following

<img src="images/NodeTutorial/deployNodeUbuntuExec.png" alt="Distelli Node.js Exec Commands" />

Copy/Paste
~~~
echo "Executing"
/usr/bin/nodejs app.js
~~~

### Re-Build Application

Because we have changed our deployment steps we need to rebuild our application. First save your changes by clicking the "Publish Manifest" button and then rebuild your application. To trigger a new build just click the wrench icon in the right hand corner! That button triggers a build of your latest code, build steps, and deployment steps.

<img src="images/NodeTutorial/deployTriggerNewBuild.png" alt="Trigger new build of Node.js Application" />

### Create Environments

Now that our application has been successfully built, it is ready to be deployed. First we will set up our environments within Pipelines. To start navigate to your application and click on the <b>Environments</b> tab.

<img src="images/Parse/parseEnvironments.png" alt="Parse Environments" />

Click the <b>Create Environment</b> link to create our first environment. Enter in the information for your Parse Beta environment:

<img src="images/Parse/parseBetaEnvironment.png" alt="Create Parse Beta Environment" />

Repeat the same process to create your Parse Production Environment. When you have finished you will have two environments for you application.

<img src="images/Parse/parseAllEnvironments.png" alt="Parse Environments" />

#### Install Agent on Servers

To add your severs to your Application's environments you need to install the Pipelines Agent on each of your servers. You will need remote access to each of your servers to complete the install. If you would like more information on installing the Pipelines agent, visit [Installing the Pipelines Agent](./agent.html). 

To add your server to your environment navigate to your Pipelines Environment, click the <b>Servers</b> tab, and then select the <b>Add Servers</b> link on the right hand side of the screen.

<img src="images/Parse/parseAddServerEnvironment.png" alt="Add Servers to a Pipelines Environment" />

A side panel will prompt you to select a Server to add to your environment. Remember we have a Beta and Production environment, so add the appropriate server to the appropriate environment. 

### Create Application Pipeline

The final step for setting-up our application in Pipelines is create our application pipelines. This feature will help you control you application and set deployment rules. Everytime we successfully build our Parse-Server application, we want it to automatically deploy to our Beta stack. To start navigate to your application in Pipelines and find the <b>App pipeline</b> section.

<img src="images/Parse/parseAppPipelinesCreate.png" alt="Parse Application Pipelines" />

Click the <b>Add Step</b> button, select your Beta environment, and click <b>Add</b>. A pipeline will be created for your Beta environment. Select the <b>Auto Deploy</b> checkbox. Now our application will automatically be deployed upon every successful build of our Parse-Server application.

<img src="images/Parse/parseAutoDeploy.png" alt="Parse Server Auto-Deploy" />

Repeat the same steps to create an additional pipeline for your Production environment. For you Production environment, <b>DO NOT</B> select the auto-deploy checkbox. We want to control when your server is deployed to our production environment.

<img src="images/Parse/parseAppPipeline.png" alt="Parse application pipeline" />

## Step 4. Deploy to Beta

Our application has been created, and build & deployment steps have been configured. Our next step is deploy our Parse-Server to our Beta Stack and begin testing. In your <b>App pipelines</b> section, click <b>Promote</b> for your Beta environment.

<img src="images/Parse/parsePromoteBeta.png" alt="Promote Parse Server to Beta" />

After your server has successfully deployed to your Beta environment, you need to create a Development version of your Parse application. To create your Development clients update your application to the latest Parse SDK, and point your application at your new Parse-Server URL. Below are code examples on how to point your client at your new server:

### Swift
~~~
let configuration = ParseClientConfiguration {
    $0.applicationId = "<b>YOUR_APP_ID</b>"
    $0.clientKey = ""
    $0.server = "http://<b><-BETA SERVER IP ADDRESS-></b>:1337/parse"
}
Parse.initializeWithConfiguration(configuration)
~~~

### Objective-C
~~~
[Parse initializeWithConfiguration:[ParseClientConfiguration configurationWithBlock:^(id<ParseMutableClientConfiguration> configuration) {
   configuration.applicationId = @"<b>YOUR_APP_ID</b>";
   configuration.clientKey = @"";
   configuration.server = @"http://<b><-BETA SERVER IP ADDRESS-></b>:1337/parse";
}]];
~~~

### Android
~~~
Parse.initialize(new Parse.Configuration.Builder(myContext)
    .applicationId("<b>YOUR_APP_ID</b>")
    .clientKey(null)
    .server("http://<b><-BETA SERVER IP ADDRESS-></b>:1337/parse")

    ...

    .build()
);
~~~

### JavaScript
~~~
Parse.initialize("<b>YOUR_APP_ID</b>");
Parse.serverURL = 'http://<b><-BETA SERVER IP ADDRESS-></b>:1337/parse'
~~~

### .NET
~~~
ParseClient.initialize(new ParseClient.Configuration {
    ApplicationId = "<b>YOUR_APP_ID</b>",
    Server = "http://<b><-BETA SERVER IP ADDRESS-></b>:1337/parse"
});
~~~

### PHP
~~~
ParseClient::initialize('<b>YOUR_APP_ID</b>', '<b>YOUR_CLIENT_KEY</b>', '<b>YOUR_MASTER_KEY</b>');
ParseClient::setServerURL('http://<b><-BETA SERVER IP ADDRESS-></b>:1337/parse');
~~~

### Test Your New Server

Once your Development client has been created. Test your new Parse-Serve with your new clients. Make sure everything is working as expected. This process can take a few days or weeks, so do not rush your testing. You want to make sure nothing in your application is broken and it works as expected.

## Step 5. Promote to Production

Once you are satisfied with your testing promote your server into production, and release your migrated Parse application to your customers. You can follow the same steps as above for your Beta environment:

1. Promote your release from your Beta environment to your Production environment
2. Point your clients to your new Production environment
3. Test your application
4. Publish your application

## Summary

Once you have completed this guide you will have:

* Migrated your Parse Application's back-end
* Created a Beta Environment for your application's back-end
* Created a Production Environment
* Published a new Parse Application using your new Back-end

The Beta environment is where you can test any changes you need to make to your application's back-end before you push it to production. This helps ensure that only fully tested features and changes are pushed to your customers.




