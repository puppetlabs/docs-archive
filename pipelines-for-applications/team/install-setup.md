---
layout: default
title: "Installing and setting up Pipelines for Applications"
--- 

In order for your organization to begin using Pipelines for Applications, you must first complete the initial installation and setup process. 

## System requirements

To run Pipelines for Applications, you'll need:

* A Linux server with at least two CPUs and eight GB of RAM (AWS m4.large)
* The ability to run a Docker image
* Network connectivity on port 8080
* A Pipelines for Applications license, available at the [Puppet licenses manager](https://licenses.puppet.com)
* A database for persisting information, either MySQL (version 5.7 or later in the 5.x series) or AWS DynamoDB
* Storage space with Amazon S3 or Artifactory for storing logs and artifacts 
* A load balancer is strongly recommended, but not required

## Set up a MySQL server for Pipelines for Applications

Follow the instructions below to create a MySQL database for use with Pipelines for Applications. 

> **Note:** If you're an AWS DynamoDB user, your DynamoDB table will be created automatically during installation. 

1. [Install MySQL](https://dev.mysql.com/downloads/) version 5.7 or later in the 5.x series on the server. 
2. Bind MySQL to `0.0.0.0` to ensure that it listens to all available networks by running `mysqld --bind-address=::` 
3. Create a new MySQL database to use with Pipelines for Applications by running `create database <DATABASE NAME>;`. 
4. Create a new MySQL database user by running `create user <USERNAME>;`. 
5. Set a password for the MySQL database user account by running `set password for '<USERNAME>' = '<MYSQL PASSWORD>';`
6. Run `grant ALL on '<DATABASE NAME>'.* to '<USERNAME>'@'%';`
7. Create a file called `envs.sh` to configure your MySQL database information. Add the following to the file, replacing the variables with the MySQL database name, username, and password you created above: 

   ```
   export DB_ENDPOINT="mysql://<YOUR SERVER’S IP ADDRESS>:3306/<DATABASE NAME>"
   export USER="<USERNAME>"
   export MYSQL_PWD="<MYSQL PASSWORD>"
   ```

8. Save and exit the file. 
9. Source the file into your environment, to load the env variables, with `source envs.sh`.
   
## Install Pipelines for Applications

Installing Pipelines for Applications requires you to pull and run a Docker container, then follow our guided setup to create the root account and configure your installation.

> **Before you begin:** [Install Docker](https://docs.docker.com/engine/installation/) on the server. 

1. Pipelines for Applications is delivered as a Docker container. Pull down the latest version by running:

`docker pull puppet/pipelines-for-applications:latest`

1. Run the image with the command appropriate to your database infrastructure, replacing the options with the appropriate values, as per the instructions below.

   **For MySQL:** 
   
   ~~~
   docker run --rm -p 8080:8080 -p 8000:8000 -p 7000:7000 -e USER -e MYSQL_PWD -e DB_ENDPOINT puppet/pipelines-for-applications:latest
   ~~~
   
   `-p 8080:8080 -p 8000:8000 -p 7000:7000`
   The three ports used by Pipelines for Applications for the web UI (8080), the backend service (8000), and the agent service (7000). You can map these ports to any port of your choosing. 
   
   `USER and MYSQL_PWD`
   Login credentials for your MySQL user. For security purposes, the database user you select should only be able to connect to this database.

   `DB_ENDPOINT`
   The mysql:// or ddb:// endpoint used to connect to your database. 

   
   **For AWS DynamoDB:** 
   
   ~~~
   docker run --rm -p 8080:8080 -p 8000:8000 -p 7000:7000 -e DB_ENDPOINT=ddb://<AWS_REGION> -e AWS_ACCESS_KEY_ID=<AWS_ACCESS_KEY> -e AWS_SECRET_ACCESS_KEY=<AWS_SECRET_KEY> puppet/pipelines-for-applications:latest
   ~~~
 
   `-p 8080:8080 -p 8000:8000 -p 7000:7000`
   The three ports used by Pipelines for Applications for the web UI (8080), the backend service (8000), and the agent service (7000). You can map these ports to any port of your choosing. 

   `DB_ENDPOINT`
   The mysql:// or ddb:// endpoint used to connect to your database. 

   `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY`
   Access and secret keys for your DynamoDB user. For security purposes, the database user you select should only be able to connect to this database.
   
   > **Important:** Make sure you generate credentials with full create, read, update, and delete permissions for DynamoDB resources.
   
1. Point your browser to `http://<YOUR_SERVER'S_IP_ADDRESS>:8080` and click **Get Started**.

## Set up Pipelines for Applications for your team

Before your team can begin using Pipelines for Applications, there are five additional system configuration tasks to complete. The Pipelines for Applications setup interface will walk you through this process. These steps are as follows.

### Create the root administrator account

1. Enter the name and email address for your Pipelines for Applications account's root administrator. This should be a person or team trusted with "superuser" root access rights. 
2. Create a username and a strong password for the root administrator account. 

   > **Caution:** Make sure to store these credentials in a safe place, as you won't be able to access your account without them. 

3. Click **Create Root Account**. 

### Upload a Pipelines for Applications license

1. Click **Select License File** and navigate to the directory where you saved the license file. 
2. Click **Upload license**.

### Configure endpoints

We recommend using endpoints on a hardware load balancer or a load-balancing proxy webserver to ensure consistency and provide a fall-back system.

1. Enter the endpoint for the Pipelines for Applications web UI, in the form of `http://<YOUR SERVER'S IP ADDRESS>:8080`. If using a load balancer, change `<YOUR SERVER'S IP ADDRESS>:8080` to your load balancer listner `IP:PORT`. If terminating SSL on your load balancer then use `https://`.
2. Enter the endpoint for the Pipelines for Applications backend service, in the form of `http://<YOUR SERVER'S IP ADDRESS>:8000`.
3. Enter the endpoint for the Pipelines for Applications agent service, in the form of `dump://<YOUR SERVER'S IP ADDRESS>:7000`. If using SSL with a load balancer, use `dumps://`.

### Set up storage

Pipelines for Applications supports storage integrations with Amazon S3 and Artifactory. We suggest allocating 100 GB of storage space for use with Pipelines for Applications. 

On the **Storage** setup screen, select your storage provider and fill in the fields as directed. 

> **Note for Artifactory users:** To generate a new Artifactory API key for the **Artifactory Access Token** field, run `curl -u admin:password http://<YOUR SERVER'S IP ADDRESS>:8081/artifactory/api/security/users/apiKey`. To retrieve an existing Artifactory API key, run `curl -X POST -u admin:password http://<YOUR SERVER'S IP ADDRESS>:8081/artifactory/api/security/apiKey`. 

### Set up integrations

The final step in the initial setup is to establish integrations with your team's existing code repositories, email, and messaging systems. You can also elect to skip this step for now by clicking **Skip this step**, and come back to it once Pipelines for Applications is up and running. 

For each integration, you must visit the service provider and follow the steps to create a custom OAuth application. You'll then enter the OAuth key and secret on the **Integrations** setup screen.

For more information see our guides to setting up [Slack](https://pipelines.puppet.com/docs/onprem/onpremise-slack) and [GCP](https://pipelines.puppet.com/docs/onprem/onpremise-gcp).

Find instructions for creating OAuth applications here:

* [BitBucket](https://confluence.atlassian.com/bitbucket/oauth-on-bitbucket-cloud-238027431.html)
* [GitHub](https://developer.github.com/apps/building-oauth-apps/creating-an-oauth-app/)
* [Google Cloud Platform](https://pipelines.puppet.com/docs/onprem/onpremise-gcp)
* [Slack](https://api.slack.com/docs/oauth)










