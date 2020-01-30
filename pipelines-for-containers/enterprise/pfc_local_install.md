---
layout: default
title: "Install Pipelines for Containers Enterprise locally with Docker"
---

If you're interested in evaluating Pipelines for Containers Enterprise, follow these steps to set up a local Docker installation of Pipelines with a trial license.

Before you set up Pipelines for Containers: 
* Make sure you've installed the latest version of <a href="https://docs.docker.com/install/" target="_blank">Docker Community Edition</a>.
* Make sure you have access to an <a href="https://aws.amazon.com/free/storage/?sc_channel=PS&sc_campaign=acquisition_US&sc_publisher=google&sc_medium=ACQ-P%7CPS-GO%7CBrand%7CDesktop%7CSU%7CStorage%7CS3%7CUS%7CEN%7CText&sc_content=s3_e&sc_detail=aws%20s3&sc_category=Storage&sc_segment=293617570035&sc_matchtype=e&sc_country=US&s_kwcid=AL!4422!3!293617570035!e!!g!!aws%20s3&ef_id=EAIaIQobChMI7L28kciC4AIVIx-tBh04NgEqEAAYASAAEgI5sPD_BwE:G:s" target="_blank">Amazon Web Services S3 bucket</a> for storing your artifacts and logs, and set up an <a href="https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html" target="_blank">API access key</a>.
* Download a Pipelines for Containers Enterprise trial license from <a href="https://licenses.puppet.com/Hestonhoffman/licenses">Puppet License Manager</a>. 

To install Pipelines for Containers Enterprise:
1. Set up the MySQL Server container to serve as the database for Pipelines.
   1. (Optional) Verify MySQL Server is not already running:
      
      ```docker ps -s```
   1. If the `mysql/mysql-server` container is already running, stop the container:
      
      ```docker stop <container id>```
   1. Download MySQL Server version 5.7.23:
      
      ```docker pull mysql/mysql-server:5.7.23```
   1. Run MySQL Server:
      
      ```docker run --rm -d -p 3306:3306 --name=mysql -e MYSQL_DATABASE=pfc -e MYSQL_USER=pfc -e MYSQL_PASSWORD=pfc -e MYSQL_ROOT_PASSWORD=pfc mysql/mysql-server:5.7.23
      ```

      > **Remember**: The container continues to run in the background until you stop it with `docker stop <CONTAINER_ID>`. Stopping the MySQL container wipes your account and configuration data.

   1. Give the container a moment to start up, and then check its status to make sure it's `healthy`: 
      
      ```docker ps -s```
1. Set up Pipelines for Containers.
   1. (Optional) Verify the Pipelines container is not already running:
      
      ```docker ps -s```
         
      1. If the `puppet/pipelines-for-containers` container is already running, stop the container:
      
         ```docker stop <container id>```
      
      1. Download the latest version of Pipelines for Containers:
         
         ```docker pull puppet/pipelines-for-containers:latest```

   1. Run Pipelines for Containers with your local IP address:
         
      ```docker run --rm -d -p 8080:8080 -p 8000:8000 -p 7000:7000 -e USER=pfc -e MYSQL_PWD=pfc -e DB_ENDPOINT=mysql://<YOUR_IP_ADDRESS>/pfc puppet/pipelines-for-containers:latest
      ```
         
    > **Remember**: The container continues to run in the background until you stop it with `docker stop <CONTAINER_ID>`.
    
1. Verify that the Pipelines tables exist in MySQL.
   1. Open the MySQL command-line in the MySQL Server container. 
         
         ```docker exec -it mysql mysql -uroot -p```
      
   1. Enter the password defined in the environment variables above: `pfc`.
   1. Use the following commands to see if Pipelines tables exist:
         
      ```use pfc;```
         
      ```show tables;```
         
      A table with the header `Tables_in_pfc` appears.
   1. Exit MySQL:
         
         ```exit```
         
1. Configure Pipelines for Containers:
   1. Open [http://localhost:8080/](http://localhost:8080/) in a browser.
   1. Click **Get Started**.
   1. Complete the fields to create a Pipelines root account.
   > **Remember**: The root account is an administrative account with full access to all resources, including the Pipelines console. This account should not be used for building and deploying containers.
   1. Upload the license file you got from <a href="https://licenses.puppet.com/Hestonhoffman/licenses" target="_blank">Puppet License Manager</a>.
   1. Enter the following endpoints:
      * **WebUI**: http://localhost:8080
      * **Backend Service**: http://localhost:8000
      * **Agent Service**: dump://localhost:7000
   1. Enter the name of your S3 bucket and complete the **AWS Access Key** and **AWS Secret Key** fields using your AWS access key. You can leave the other fields in this section blank.
   1. Enter keys for any applications you'd like to integrate with Pipelines. Leave these fields blank if you don't want to set up integrations. 
   1. After you complete the **Integrations** section, Pipelines uploads the Pipelines agent to your S3 bucket. 
   1. On the **Single Sign-on** screen, click **Skip this step**.
1. Log out of Pipelines and click **Create a free account** to create a standard account. You can use this account for your builds and deployments.

**Related Topics**:
* For more information about application integrations, see [Integrations](). 
* For information about updating your license, see [Updating a Pipelines license]().
* For information about build hardware, see [Build Hardware]().
