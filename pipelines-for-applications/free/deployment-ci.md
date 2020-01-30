---
layout: default
title: "Auto-deploy from your CI platform"
---

With Pipelines, you can deploy to your server or environment directly from your Continuous Integration (CI) systems. In this guide, we'll review how to set up Pipelines with CI to deploy directly to server(s).

**Note:** Pipelines provides continuous integration and continuous deployment. 


The following steps must occur:

1. Create the application in your Pipelines account.
1. Add an environment to the application.
1. Add a server to the environment.
1. Commit the Pipelines manifest to the repo.
1. Commit the Pipelines credentials file to the repo.
1. Add the deploy command to the CI system.

Make sure you have signed up for a [Pipelines account](https://pipelines.puppet.com/signup) before beginning.

### 1. Create the Application in your Pipelines account.


To push and deploy an application using Pipelines, the application must first be created. See [Creating an Application](./application-create.html).

### 2. Add an Environment to the Application.

Applications are deployed to environments. Environments contain servers. Before you can deploy your application you must first create an environment for the application. See [Creating an Environment](./environment.html).

### 3. Add a Server to the Environment.

To add a server to the environment you just created, follow the instructions [Adding Servers to an Environment](./environment.html).

**Note:** Adding servers to environments can be automated with the distelli.yml credentials file. For more information, see [Distelli distelli.yml Usage](./distelliyml.html).


### 4. Commit the Pipelines Manifest to the Repo.

For an application to successfully deploy, it requires a `distelli-manifest.yml` file. The contents of this file vary based on the application and requirements. There are many examples available in:

* Puppet Pipelines deployment tutorials (see the navigation to the left)
* [Pipelines Manifest Library of Examples](./manifest.html)

And a complete reference guide at [Pipelines Manifest](./manifest.html).

The first line of this file is:

~~~
&lt;username&gt;/&lt;application_name&gt;:
~~~


The **username** is your Pipelines username (see [Finding Your Pipelines Username](./account.html).
The **application_name** is the name of the application you created in Step 1 above.
Once this file is created and working as expected, it needs to be checked into the repository with the application.

### 5. Commit the Credentials File to the Repo.

When using the Pipelines CLI with a properly configured `~/distelli.yml` there will not be a prompt for email credentials. This will allow automated execution of Pipelines CLI commands

For information on creating the `~/distelli.yml` and finding the DistelliAccessToken and DistelliSecretKey see [Distelli distelli.yml Usage](./distelliyml.html).

~~~
DistelliAccessToken: '12345678901234567890123456'
DistelliSecretKey: '1234567890123456789012345678901234567'
~~~

Once this file is created, it needs to be checked into the repository with the application.

### 6. Add the Deploy Command to the CI System.

When the CI build process is complete and accepted, the CI system will need to execute the following commands:
Copy the distelli.yml file to the build user home directory.

~~~
cp -f distelli.yml ~/
~~~

Get the Pipelines CLI.

~~~
wget -qO- https://pipelines.puppet.com/download/client | sh
~~~

Authenticate the Pipelines CLI.

~~~
/usr/local/bin/distelli login -conf ~/distelli.yml
~~~

Deploy the application to environment ENVIRONMENT.

~~~
/usr/local/bin/distelli deploy -description "auto deploy from CI" -quiet -yes -env ENVIRONMENT
~~~


For more information on the Pipelines CLI `deploy` command see [Pipelines CLI](./cli-command.html).

#### Travis CI

Update the .travis.yml file to add a deploy step that will deploy to your environment via Pipelines. Add an `after_success` section to your `.travis.yml` file with the following contents:

~~~
after_success:
  - cp -f distelli.yml ~/
  - wget -qO- https://pipelines.puppet.com/download/client | sh
  - /usr/local/bin/distelli login -conf ~/distelli.yml
  - /usr/local/bin/distelli deploy -q -y -m "Deployment from Travis-CI" --env <ENVIRONMENT>
~~~


#### Jenkins CI

Update Jenkins to add a build step that will deploy to your servers via Pipelines. Add a new Execute Shell build step with the following contents:

~~~
cp -f distelli.yml ~/
wget -qO- https://pipelines.puppet.com/download/client | sh
/usr/local/bin/distelli login -conf ~/distelli.yml
/usr/local/bin/distelli deploy -q -y -m "Deployment from Jenkins" --env <ENVIRONMENT>
~~~


#### Codeship

Update Codeship to add a Custom Script step that will deploy to your servers via Pipelines. Add a custom script step to Codeship with the following contents:

~~~
cp -f distelli.yml ~/
wget -qO- https://pipelines.puppet.com/download/client | sh
/usr/local/bin/distelli login -conf ~/distelli.yml
/usr/local/bin/distelli deploy -q -y -m "Deployment from Codeship" --env <ENVIRONMENT>
~~~


