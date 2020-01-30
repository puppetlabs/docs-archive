---
layout: default
title: "Docker run Puppet Container Registry with MySQL"
---

This document will walk through a basic scenario to get you started running Puppet Container Registry Docker Registry containers with Docker.

This document does not take into consideration the obfuscation of passwords or secure strings.

<h3>Requirements</h3>

Puppet Container Registry requires a large store to put docker layers. Currently Puppet Container Registry supports AWS S3 and Filesystem. The example in this document will use Filesystem.

Puppet Container Registry requires a database. This must be MySQL 5.7. The example in this document will use the mysql/mysql:5.7 docker image.


<h3>Setup a server</h3>

First you will need a server to run the Docker engine and the containers on. For this example you will be using a local filesystem to store docker layers, so consider that in the sizing of the disk. Also realize that the MySQL database will be sitting on this server too.


<h5>Install Docker</h5>

The server must have the Docker engine to run the docker containers. You can find instructions on installing docker at <a href="https://docs.docker.com/engine/installation/">Docker Engine Installation</a>.


<h5>Create a Puppet Container Registry directory</h5>

This directory can be created anywhere in the filesystem. I created mine at <code>/europa</code>.

The balance of this document will refer to this directory as <b>EUROPA_ROOT_DIR</b>.


<h5>Create a Europa repo directory</h5>

For this example we will create the EUROPA_REPO_DIR under EUROPA_ROOT_DIR. I created mine as <code>/europa/repo</code>.


<h5>Create the MySQL volume mount directory</h5>

For this example we will create the MYSQL_MOUNT_DIR under EUROPA_ROOT_DIR. I created mine as <code>/europa/mysql</code>.


<h5></h5>


<h3>Create `mysql.env`</h3>

This file will carry important environment variables that are used to initialize values in your mysql container. I created mine as <code>/europa/mysql.env</code>.

~~~
MYSQL_ROOT_PASSWORD=setthisyourself
MYSQL_DATABASE=europaDB
MYSQL_USER=distelli
MYSQL_PASSWORD=Pa55w0rd
MYSQL_ROOT_HOST=172.17.0.1
~~~

<ul>
  <li><b>MYSQL_ROOT_PASSWORD</b> - Set this to whatever you feel is appropriate.</li>
  <li><b>MYSQL_DATABASE</b> - This is the name of the database that Puppet Container Registry will use. On first run, this database will be created.</li>
  <li><b>MYSQL_USER</b> - This is the user name that has full access to the database created above.</li>
  <li><b>MYSQL_PASSWORD</b> - This is the password associated with the MYSQL_USER above.</li>
  <li><b>MYSQL_ROOT_HOST</b> - This is the address that is allowed access to connect to the MySQL instance. The address I have included here <code>172.17.0.1</code> should work as this is the docker bridged network IP.</li>
</ul>


<h3>Create `europa.env`</h3>

This file will carry important environment variables that are used to initialize values in your europa container. I created mine as <code>/europa/europa.env</code>.

~~~
EUROPA_DB_ENDPOINT=mysql://172.17.0.2:3306/europaDB
EUROPA_DB_USER=distelli
EUROPA_DB_PASS=Pa55w0rd
~~~

<ul>
  <li><b>EUROPA_DB_ENDPOINT</b> - The endpoint to the MySQL instance. This is in the format of <code>mysql://ADDRESS:PORT/DATABASE</code><br>This example assumes you are running no other containers on this server.<br>This further assumes that the MySQL container will always be started first and get the first IP address of 172.17.0.2.</li>
  <li><b>EUROPA_DB_USER</b> - The user that has full access to the Puppet Container Registry Database. Set in mysql.env.</li>
  <li><b>EUROPA_DB_PASS</b> - The password for the above user. Set in mysql.env.</li>
</ul>


<h3>Start the MySQL container</h3>

You will want to always start the MySQL container first. The syntax to start the MySQL container is:

~~~
docker run --rm --name mysql -v MYSQL_MOUNT_DIR:/var/lib/mysql --env-file mysql.env -p 3306:3306 -d mysql/mysql-server:5.7
~~~

Note the MYSQL_MOUNT_DIR must be replaced with the directory you created earlier for this. Because I used <code>/europa/mysql</code>, my docker run mysql command looks like this:

~~~
docker run --rm --name mysql -v /europa/mysql:/var/lib/mysql --env-file mysql.env -p 3306:3306 -d mysql/mysql-server:5.7
~~~


<h3>Start the Puppet Container Registry container</h3>

You will start the Puppet Container Registry container after the MySQL container.

~~~
docker run --rm --name europa -v EUROPA_REPO_DIR:EUROPA_REPO_DIR --env-file europa.env --link mysql:mysql -p 80:80 -p 443:443 puppet/puppet-container-registry-enterprise:latest
~~~

Note the EUROPA_REPO_DIR must be replaced with the directory you created earlier for this. Because I used <code>/europa/repo</code>, my Puppet Container Registry run command looks like this:

~~~
docker run --rm --name europa -v /europa/repo:/europa/repo --env-file europa.env --link mysql:mysql -p 80:80 -p 443:443 puppet/puppet-container-registry-enterprise:latest
~~~

The Puppet Container Registry container will log to the screen.

<h3>Stop the Puppet Container Registry container</h3>

You can stop the Puppet Container Registry container by pressing <b>CTRL-C</b>.

You can also stop the Puppet Container Registry container by running the command <code>docker stop europa</code>.

<h3>Stop the MySQL container</h3>

You can stop the MySQL container by running the command <code>docker stop mysql</code>

As long as you use the same docker volume <code>-v MYSQL_MOUNT_DIR:/var/lib/mysql</code> the database will persist between running the MySQL container.

<h3>Starting fresh</h3>

If at any point you wish to completely flatten the Puppet Container Registry install and start fresh, you can by:

<ol>
  <li>Stop the Puppet Container Registry container.</li>
  <li>Stop the MySQL container.</li>
  <li>Delete the MYSQL_ROOT_DIR. <code>sudo rm -Rf /europa/mysql</code></li>
  <li>Delete the contents of the EUROPA_REPO_DIR. <code>sudo rm -Rf /europa/repo/*</code></li>
</ol>

Now restart the containers per the instructions above and you will start with a fresh new database and all the local repository images will be gone.







