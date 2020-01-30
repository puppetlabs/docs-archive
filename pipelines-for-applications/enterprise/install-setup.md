---
layout: default
title: "Installing Pipelines Enterprise"
--- 

Follow the steps below to install Pipelines Enterprise.

Pipelines runs as a Docker container on your server. For data persistence, use a MySQL 5.7 container or Amazon Web Services (AWS) DynamoDB. To store artifacts and logs, use an Artifactory container or an AWS S3 bucket.

Before you install Pipelines:
* If you plan to run Pipelines with MySQL 5.7 and Artifactory containers, you need a Linux server with at least two CPUs and 8 GB of RAM.
* Make sure you have a valid Pipelines Enterprise License. 
* Make sure you've installed the <a href="https://docs.docker.com/install/linux/docker-ce/ubuntu/" target="_blank">latest version of Docker</a>.
* If you plan on using an AWS S3 bucket or AWS DynamoDB, make sure you've set up an <a href="https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html" target="_blank">API access key</a>.

For high-availability and redundancy, work with your network administrator to set up a load balancer and DNS entries for each Pipelines endpoint:

<table style="width:100%">
  <tr>
    <th>Protocol</th>
    <th>Example domain and port mappings</th> 
    <th>Pipelines service and port</th>
  </tr>
  <tr>
    <td>HTTP or HTTPS</td>
    <td>http://www.domain:80/443 or
https://www.domain:80/443</td> 
    <td>Web interface on port 8080</td>
  </tr>
  <tr>
    <td>HTTP or HTTPS</td>
    <td>http://login.domain:80/443 or
https://login.domain:80/443</td> 
    <td>Backend/login service on port 8000</td>
  </tr>
  <tr>
    <td>TCP</td>
    <td>dump://ags.domain:??/443 or
dumps://ags.domain:??/443</td>
    <td>Pipelines agent on port 7000</td>
  </tr>
</table>

## Set up environments for your containers

Set up `.env` files for your containers:
1. If you're using MySQL: 
   1. Create `pipelines.env` with the following environment variables:
      * `DB_ENDPOINT=mysql://172.17.0.1:3306/PipelinesDB`
        `172.17.0.1` is the host address for the MySQL container.
      * `USER=pipelines`
      * `MYSQL_PWD=password`
   1. Create `mysql.env` with the following environment variables.
      * `MYSQL_ROOT_PASSWORD=password`
      * `MYSQL_DATABASE=PipelinesDB`
      * `MYSQL_USER=pipelines`
      * `MYSQL_PASSWORD=password`
      * `MYSQL_ROOT_HOST=172.17.0.1`
      
   > **Note**: You can replace each `password` value with a secure password, but keep in mind that the password for `MYSQL_PASSWORD`, or  must match `MYSQL_PWD`.
    
1. If you're using DynamoDB: 
   1. Create `pipelines.env` with the following environment variables:
      * `DB_ENDPOINT=ddb://<YOUR REGION>` or `DB_ENDPOINT=ddb://<HOST IP ADDRESS>:<PORT>`
      * `TABLE_NAME_FORMAT=%s-<YOUR REGION>-<YOUR SCOPE>`
        
        The DynamoDB printf format used for table names.
      * `AWS_ACCESS_KEY_ID=<YOUR KEY> or AWS_ACCESS_KEY_ID=INSTANCE`
        
        Your DynamoDB AWS access key ID. If the value is INSTANCE, Pipelines will obtain the credentials from the local computer based on the IAM role.
      * `AWS_SECRET_ACCESS_KEY=<YOUR SECRET ACCESS KEY>` or `AWS_SECRET_ACCESS_KEY=INSTANCE`
        
        Your DynamoDB AWS secret access key. If the value is INSTANCE, Pipelines will obtain the credentials from the local computer based on the IAM role.
      * `PROFILE_NAME=<YOUR PROFILE>`
        
        The value used for defining credentials in the /home/k8sdash/.aws/credentials file.
   1. (Optional) Add the following environment variable if DynamoDB needs to connect through a proxy:
      * `DB_PROXY=<YOUR PROXY URL>`

## Download and run the containers

To install Pipelines, set up a MySQL Server 5.7 container, and the latest Pipelines container. 

If you're storing your artifacts and logs with Artifactory, set up an Artifactory open source 5.10.2 container. If you're using an AWS S3 bucket, you don't need the Artifactory container. 

1. (Optional) If you're using MySQL, set up a MySQL Server 5.7 container.
   1. Create a local `pipelines/mysql` directory. This is the directory you use to map a MySQL volume for data persistence:
	  
	  ```
	  mkdir pipelines
mkdir pipelines/mysql
      ```
   
   1. Download MySQL Server version 5.7:   
      
      ```
      sudo docker pull mysql/mysql-server:5.7
      ```
   
   1. Run MySQL Server:

      ```
      sudo docker run --rm --name mysql -v /home/ubuntu/pipelines/mysql:/var/lib/mysql --env-file mysql.env -p 3306:3306 -d mysql/mysql-server:5.7
      ```

   1. After you've given the container a moment to start up, check its status to make sure it's `healthy`:
          
      ```
      sudo docker ps -s
      ```
     
1. (Optional) If you're using Artifactory, set up an Artifactory 5.10.2 open source container. 
   1. Create a local `pipelines/artifactory` directory. This is the directory you use to map the Artifactory volume for data persistence.
      
      ```
      mkdir pipelines/artifactory
      ```
      
   1. Download the Artifactory container:
      
      ```
      sudo docker pull docker.bintray.io/jfrog/artifactory-oss:5.10.2
      ```
      
   1. Run Artifactory:

      ```
      sudo docker run --rm --name artifactory -v /home/ubuntu/pipelines/artifactory:/var/opt/jfrog/artifactory -p 8081:8081 -d docker.bintray.io/jfrog/artifactory-oss:5.10.2
      ```
      
   1. After you've given the container a moment to start up, check its status to make sure it's running.

      ```
      sudo docker ps -s
      ```
      
   1. Build an Artifactory repository.
      1. Find your server's public IP address:
      
         ```
         sudo curl ident.me
         ```

      1. In a browser, enter your server's public IP address followed by the port `:8081`. For example: `192.168.0.1:8081`. Artifactory loads.
      1. Enter an administrator password and click **Next**.
      1. Click **Skip** to skip the proxy set-up screen.
      1. Select the **Generic** repository type and click **Create**. Artifactory creates a repository named `generic-local`.
      1. Click **Finish**.
   1. Generate an API key for Artifactory.
      1. Use the following command with your Artifactory admin password to generate an API key: 
         ```
         sudo curl -X POST -su admin:<YOUR ARTIFACTORY ADMIN PASSWORD> http://localhost:8081/artifactory/api/security/apiKey
         ```
               
         > **Note**: You can only use this command one time to create a key. If you need to reveal the key again in the future, use `sudo curl -X GET -su admin:<YOUR ARTIFACTORY ADMIN PASSWORD> http://localhost:8081/artifactory/api/security/apiKey`
          
      1. Copy the API key and save it somewhere safe. You need it to set up your Pipelines root account. 
1. Set up a Pipelines container.
   1. Download the latest version of Pipelines:
      
      ```
      sudo docker pull puppet/pipelines:latest
      ```
   1. Run Pipelines:

      ```
      sudo docker run --rm --name pipelines --env-file pipelines.env -p 8080:8080 -p 8000:8000 -p 7000:7000 -d puppet/pipelines:latest
      ```
      
   1. After you've given the container a moment to start up, check its status to make sure it's running: 
     
      ```
      sudo docker ps -s
      ```
     
   1. To make sure the container is running without errors, view the logs:
      
      ```
      sudo docker logs pipelines -f
      ```
      
1. (Optional) If you're using MySQL, verify that the Pipelines tables exist in MySQL.
   1. Open the MySQL command-line in the MySQL Server container. 
      
      ```
      sudo docker exec -it mysql mysql -uroot -p
      ```
      
   1. Enter the MySQL password defined in the `MYSQL_PASSWORD` environment variable above.
   1. Use the following commands to see if Pipelines tables exist:
      
      ```
      use PipelinesDB;
show tables;
      ```
   
      A table with the header `Tables_in_pfc` appears.
   1. Exit MySQL:
   
      ```
      exit
      ```

## Configure Pipelines Enterprise

Before you use Pipelines, set up a root account, upload your license, and configure your endpoints and storage.

1. In a browser, use the provisioned load-balancer for port 8080, or enter your server's public IP address followed by the port `:8080`. For example: `192.168.0.1:8080`.
1. Set up a root account. The root account is an administrative account with full access to all resources, including the Pipelines console. This account should not be used for building and deploying containers.
   1. Click **Get Started**.
   1. Complete the **Name**, **Email**, **Username**, and **Password** fields to create a Pipelines root account.
1. Upload your license.
   1. Select your license file and click **Upload License**. 
   1. Read through the software license agreement and click **Accept** > **Next**. 
1. Configure your endpoints:
   1. If you've set up DNS entries and load balancers, enter the addresses for each of the services, for example:
      * **WEbUI**: `https://www.domain:8080`
      * **Backend Service**: `https://login.domain:8000`
      * **Agent Service**: `dumps://ags.domain:7000`
   1. If you haven't set up DNS entries and load balancers, use your server's public address:
      * **WEbUI**: `192.168.0.1:8080`
      * **Backend Service**: `192.168.0.1:8000`
      * **Agent Service**: `192.168.0.1:7000`
   1. Click **Save Endpoint Settings**.
1. Configure your storage.
   1. If you're using an S3 bucket to store your artifacts and logs:
      1. Select **Amazon S3**.
      1. Enter the name of your S3 bucket.
      1. Enter your **AWS Access Key** and **AWS Secret Key**.
      1. (Optional) If you're using an S3 emulator, enter your **S3 Bucket Endpoint**.
      1. (Optional) If your S3 requests need to go through a proxy, enter your S3 **Proxy Endpoint**.
      1. (Optional) If you need to use a subdirectory within your S3 bucket, enter a **Path Prefix**.  
      1. Click **Save Storage Settings**.
   1. If you're using Artifactory to store your artifacts and logs:
      1. Select **Artifactory**.
      1. Enter the **Artifactory Generic Binary Repository Name** of the generic repository that you created earlier. For example, `generic-local`.
      1. Enter your **Artifactory Endpoint**. This your server address followed by port `8081`. For example, `192.168.0.1:8081`.
      1. Enter your **Artifactory Access Token**. This is the API key that you saved earlier. If you need to recover the key, use the following command:
         
         ```
         sudo curl -X GET -su admin:<YOUR ARTIFACTORY ADMIN PASSWORD> http://localhost:8081/artifactory/api/security/apiKey
         ```
         
      1. Leave **Path Prefix** blank unless you've changed the location of your Artifactory repository.
      1. Click **Save Storage Settings**.
1. (Optional) Configure your integrations and click **Save Settings**. Once you're done configuring your integrations, Pipelines uploads the Pipelines agent to your object store.
   > **Note**: You can edit your integrations later from the administrator console. For more information on using the administrator console to set up integrations, see [Pipelines administrator console](../enterprise/admin-console.md).
1. Click **Finish**. 
1. Log out of Pipelines and click **Create an account** to create a non-root account to use for your builds and deployments.

Related topics:
* For more information about generating a license for Pipelines, see the [Puppet license manager](https://licenses.puppet.com/).
* For more information about updating your license, see [Updating a Pipelines Enterprise license](https://puppet.com/docs/pipelines-for-containers/enterprise/pfc_update_license.html).










