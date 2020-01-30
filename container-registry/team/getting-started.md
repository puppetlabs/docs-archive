---
layout: default
title: "Getting Started with Puppet Container Registry"
---

Puppet Container Registry is a Docker container registry, and is available at <code>puppet/puppet-container-registry-enterprise</code>.

<h2>Download Puppet Container Registry</h2>

To get started, 

1. Download Puppet Container Registry Enterprise Edition with a `docker pull` command
2. Download a trial license by signing up at [licenses.puppet.com](https://licenses.puppet.com), clicking "Get License", and clicking "Trial License" for Container Registry. Download the license file and enter it when prompted.

~~~
docker pull puppet/puppet-container-registry-enterprise:latest
~~~

<h2>Docker image storage</h2>

Puppet Container Registry can store local repository Docker images either in AWS S3 or on a filesystem.

If using a <b>filesystem</b>, and you want to keep your Docker images between Puppet Container Registry container runs, you must use a mounted volume to persist the data. Make sure this volume (directory) exists before starting the container and ensure you start the container with the <code>-v</code> option.

<h2>Starting Puppet Container Registry</h2>

To start the Puppet Container Registry container, you must tell it where to store persistent data. Puppet Container Registry works with AWS DynamoDB and MySQL 5.7.23. If you're trying Puppet Container Registry for the first time, use MySQL - it's easier to get started and clean up afterward.

On first run, Puppet Container Registry will create any necessary tables and indexes.

Use <code>docker run</code> to start the Puppet Container Registry container, using the flags below as appropriate.

### Puppet Container Registry with MySQL 

> **Note:** The MySQL database must exist before Puppet Container Registry can use it. To run an ephemeral MySQL database using Docker, run:
```
docker run --rm -d -p 3306:3306 --name=mysql -e MYSQL_DATABASE=pcr -e MYSQL_USER=pcr -e MYSQL_PASSWORD=password -e MYSQL_ROOT_PASSWORD=password mysql/mysql-server:5.7.23
```

Wait for the MySQL container to initialize. This should take roughly 30 seconds. You can use `docker ps` to verify that the container's status has transitioned from "starting" to "healthy."

To start Puppet Container Registry, run:

~~~
docker run --rm --name pcr -e EUROPA_DB_ENDPOINT=mysql://172.17.0.2:3306/pcr -e EUROPA_DB_USER=pcr -e EUROPA_DB_PASS=password --link mysql:mysql -p 8080:80 -p 443:443 puppet/puppet-container-registry-enterprise
~~~

This container will run in the foreground and log to the console.

### Puppet Container Registry with AWS DynamoDB 

Start your container by passing your environment variables directly with the `-e` flag. Your `docker run` command might look like this:

~~~
docker run --rm --name pcr -e EUROPA_DB_ENDPOINT=ddb://us-west-2 -e EUROPA_DB_USER=AKIAIOSFODNN7EXAMPLE -e EUROPA_DB_PASS=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY -p 8080:80 -p 443:443 puppet/puppet-container-registry-enterprise
~~~

This container will run in the foreground and log to the console.

<h2>Configure Puppet Container Registry</h2>

Navigate to [http://localhost:8080](http://localhost:8080) in a browser.

<h3>Configure storage</h3>
Select File System, then enter "." (without no quotes) for Storage Root Directory. This is  a valid configuration only for evaluation purposes - the data won't be persisted after the Docker container exits. For persistence, mount a volume into the container and then set that path during configuration.

<h3>Create a root account</h3>
Enter Email and a password. The first account created is exclusively for container registry administration and cannot create repositories, so this should not be your user account.

<h3>Log in and enter a license</h3>

Click "Login" and enter the credentials for the root account you just created. You will be prompted to upload a license. Click Upload, select the license file you just downloaded from [licenses.puppet.com](https://licenses.puppet.com), click Submit, and then accept the license after reviewing it.

No further configuration is immediately necessary, so log out by clicking the blue logout button in the upper right corner.

<h3>Create a user account</h3>
Click "Sign Up" and register a normal user you will use to evaluate Puppet Container Registry. Set Username to "testuser" to be consistent with the sample commands below. You will immediately be logged in as that user.

<h3>Create your first repository</h3>

1. Click "Create Token", then click the copy link to the right of the login instructions
2. Paste that command into a terminal to log Docker in to Puppet Container Registry
3. Click "Create a Local Repository", name it "hello-world", and click create

<h3>Push a first container into the repository</h3>

~~~
docker pull hello-world
docker tag hello-world localhost:8080/testuser/hello-world
docker push localhost:8080/testuser/hello-world
~~~

Click the "Tags" tab and observe that the container image  you pushed is now available. You can pull the container by running:

~~~
docker pull localhost:8080/testuser/hello-world
~~~

<h2>Stopping Puppet Container Registry</h2>
When you're done, you can stop the Puppet Container Registry container by pressing <b>CTRL-C</b>. If you started a MySQL container, you should also run <code>docker kill mysql</code> to stop that container.
