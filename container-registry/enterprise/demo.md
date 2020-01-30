---
layout: default
title: "Self-guided demonstration of Puppet Container Registry"
---

If you're interested in evaluating Puppet Container Registry Enterprise Edition for possible production use, follow these steps to obtain a trial license and set up an ephemeral instance that does not save any data. If you've already installed Docker, completing this self-guided demonstration should take less than 30 minutes. 

> **Before you begin,** make sure you've installed [Docker](https://docs.docker.com/install/).

## Download a trial license

To obtain a free 30-day trial license of Puppet Container Registry Enterprise Edition, go to the [Puppet Pipelines License Manager](https://licenses.puppet.com/) and sign up for a Puppet Pipelines account. If you already have an account, use your credentials to sign in. 

## Set up Puppet Container Registry

To get started with Puppet Container Registry Enterprise Edition, you must start two containers and log in. 

1. Start a MySQL Docker container. On the command line, run:

   ```
   docker pull mysql/mysql-server:5.7.23 
   docker run --rm -d -p 3306:3306 --name=mysql -e MYSQL_DATABASE=pcr -e MYSQL_USER=pcr -e MYSQL_PASSWORD=password -e MYSQL_ROOT_PASSWORD=password mysql/mysql-server:5.7.23
   ```

2. Wait roughly 30 seconds for the MySQL container to initialize. 
3. Start a Puppet Container Registry Docker container. On the command line, run:

   ```
   docker run --rm --name pcr-ce -e EUROPA_DB_ENDPOINT=mysql://172.17.0.2:3306/pcr -e EUROPA_DB_USER=pcr -e EUROPA_DB_PASS=password --link mysql:mysql -p 8080:80 -p 8443:443 puppet/puppet-container-registry-enterprise:latest
   ```

1. In your web browser, navigate to `http://localhost:8080/`. This is the Puppet Container Registry web UI, where you can view recent activity, manage your repositories, and create [promotion pipelines](./pipeline.html).
1. Select **File System**. Enter `.` into the **Storage Root Directory** field, and click **Save**. 

### Create a root account and upload your trial license

On the next screen, you'll be prompted to create a root account. 

1. Enter a user email and password for the root account. This email address will not be verified, so feel free to use an easy-to-remember address, such as root@pcr.com. 
1. Click **Login** and enter the root account credentials you just created. Click **Login**.
1. On the licenses screen, click **Upload** and select your license file, then click **Submit License**. Read and accept the software license agreement. 

Your license is now displayed on the **Licenses** screen. 

### Create a user account and log into Puppet Container Registry

The root account manages Enterprise Edition features including SAML integration and user accounts. To work with containers and repositories, you must create a user account. 

1. Log out of the root user's account. Click **Logout** in the upper right corner of the web UI. 
1. Click **Sign Up** and create a user account with a different email address from the root user.

    > **Tip:** For purposes of this demonstration, we suggest using `demo` as the username for the user account. If you set `demo` as your username, you can copy and paste the commands in the rest of this demonstration without alteration.
    
1. Before you can begin pushing containers, you must log into Puppet Container Registry. Click **Create Token**, and copy the `docker login` command that appears. This contains your user token.
1. On the command line, run the `docker login` command. You should see a `Login Succeeded` message. 

You're now ready to use Puppet Container Registry!

## Build a new container and push it to Puppet Container Registry

Next, we'll walk through the process of creating a new container for a "Hello, world" application and pushing it to Puppet Container Registry. 

> **Note:** If you didn't choose `demo` as your username, substitute your username for `demo` in the following commands.

1. First, create a local copy of the application. On the command line, run:

   ```
   git clone https://github.com/puppetlabs/gohello.git
   cd gohello
   ```

1. Next, create the new container by running: 

   ```
   docker build -t localhost:8080/demo/hello:latest .
   ```
    
1. Push the new container to Puppet Container Registry by running:

   ```
   docker push localhost:8080/demo/hello:latest
   ```
   
1. You can now view the new container in Puppet Container Registry. In your web browser, navigate to `http://localhost:8080/demo/repositories/hello`. Click **Events** to see the push event. 

## Pull the container from Puppet Container Registry

Finally, we'll walk through the process of pulling a container from Puppet Container Registry. 

> **Note:** If you didn't choose `demo` as your username, substitute your username for `demo` in the following commands.

1. Delete the local container you created in the previous section. On the command line, run:

   ```
   docker rmi localhost:8080/demo/hello:latest
   ```

1. Use `docker run` to pull the image and execute it by running:

   ```
   docker run --rm -p8081:8080 -d --name hellopcr localhost:8080/demo/hello:latest
   ```

1. Confirm that the application is running. On the command line, run:

   ```
   curl localhost:8081/hello
   ```

## Clean up the demo environment and next steps

That's it! You've now seen how to perform basic container push and pull events with Puppet Container Registry. To clean up this demonstration environment, run the following commands to stop the containers:

~~~
docker kill hellopcr
docker kill mysql 
docker kill pcr-ce
~~~

If you're eager to learn more about Puppet Container Registry, or ready to start using it in production, we recommend the following next steps:

* Read our guide to [getting started with Puppet Container Registry](./getting-started.html)
* Learn more about [promotion pipelines](./pipeline.html)
