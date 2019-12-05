---
layout: default
title: "Enabling PE and external PostgreSQL without root access"
canonical: "/pe/latest/install_external_postgresql.html"
---

> **WARNING**: The procedure in this document has been created and verified by Puppet's professional services engineers. It has not gone through the usual quality assurance process. Before undertaking any of these procedures, we strongly advise that you review them thoroughly and test them in non-production environments. Beyond validation from our professional services engineers, Puppet does not offer support for any of these procedures outside of a Professional Services engagement.

Even if you don't have root privileges, you can configure an external PostgreSQL database to work with PE, including configuring SSL between PE and the external database. This allows you to use services such as Amazon RDS PostgreSQL, where you usually don't have access to the file system or the ability to manage certificates. 

## Before you start
Before you start, obtain the following:

- The complete certificate authority certificate chain for the external party CA, in PEM format.
- The DNS-addressable name, username, and password for the external PostgreSQL database.

## Step 1: Create external PostgreSQL instance 
 
Create the external PostgreSQL instance, and, if you haven't already, retrieve the necessary certificate chain and credentials from your database administrator.

For example, for RDS, the root certificate is available [here](https://s3.amazonaws.com/rds-downloads/rds-ca-2015-root.pem).

## Step 2: Create PE databases on the PostgreSQL instance

1. Log in to the external PostgreSQL instance with the client of your choice. 

2. Run commands similar to the following to create the PE databases:

~~~SQL
CREATE USER "pe-puppetdb" PASSWORD '<PASSWORD>';
GRANT "pe-puppetdb" TO <ADMIN USER>;
CREATE DATABASE "pe-puppetdb" OWNER "pe-puppetdb" 
ENCODING 'utf8' LC_CTYPE 'en_US.utf8' LC_COLLATE 'en_US.utf8' template template0;

CREATE USER "pe-orchestrator" PASSWORD '<PASSWORD>';
GRANT "pe-orchestrator" TO <ADMIN USER>;
CREATE DATABASE "pe-orchestrator" OWNER "pe-orchestrator" 
ENCODING 'utf8' LC_CTYPE 'en_US.utf8' LC_COLLATE 'en_US.utf8' template template0;

CREATE USER "pe-activity" PASSWORD '<PASSWORD>';
GRANT "pe-activity" TO <ADMIN USER>;
CREATE DATABASE "pe-activity" OWNER "pe-activity" 
ENCODING 'utf8' LC_CTYPE 'en_US.utf8' LC_COLLATE 'en_US.utf8' template template0;

CREATE USER "pe-classifier" PASSWORD '<PASSWORD>';
GRANT "pe-classifier" TO <ADMIN USER>;
CREATE DATABASE "pe-classifier" OWNER "pe-classifier" 
ENCODING 'utf8' LC_CTYPE 'en_US.utf8' LC_COLLATE 'en_US.utf8' template template0;
    
CREATE USER "pe-rbac" PASSWORD '<PASSWORD>';
GRANT "pe-rbac" TO <ADMIN USER>;
CREATE DATABASE "pe-rbac" OWNER "pe-rbac" 
ENCODING 'utf8' LC_CTYPE 'en_US.utf8' LC_COLLATE 'en_US.utf8' template template0;
 
\c "pe-rbac"
CREATE EXTENSION citext;
CREATE EXTENSION pg_trgm;
CREATE EXTENSION plpgsql;

\c "pe-orchestrator"
CREATE EXTENSION citext;
CREATE EXTENSION pg_trgm;
CREATE EXTENSION plpgsql; 

\c "pe-puppetdb"
CREATE EXTENSION citext;
CREATE EXTENSION pg_trgm;
CREATE EXTENSION plpgsql;
CREATE EXTENSION pgcrypto;
    
\c "pe-classifier"
CREATE EXTENSION citext;
CREATE EXTENSION pg_trgm;
CREATE EXTENSION plpgsql;

\c "pe-activity"
CREATE EXTENSION citext;
CREATE EXTENSION pg_trgm;
CREATE EXTENSION plpgsql;
~~~

## Step 3: Install Puppet Enterprise
  
Follow the documentation to [install PE](./install_basic.html#choose-an-installation-method).

**NOTE**: If you use the web-based installer, remember to choose the external PostgreSQL option. Skip the instructions for creating PostgresSQL databases, because you have already done this in Steps 1 and 2.
 
## Step 4: Establish SSL between PE and external PostgreSQL instance

In this step, you will establish an SSL connection between PE and your external PostgreSQL instance.

1. Log in to the nodes assigned to the PE master, PE console, and PuppetDB components and stop the Puppet agent service.

   `/opt/puppetlabs/puppet/bin/puppet resource service puppet ensure=stopped`
   
   **Note**: For a split install, stop the Puppet agent service on the Puppet master, PE console, and PuppetDB nodes. For a monolithic install, you only need to stop the service on the Puppet master.
     
2. Create a location on both the PE console and PuppetDB nodes (or on the Puppet master in a monolithic install) to store the CA cert from the external PostgreSQL instance (for example, `/etc/puppetlabs/puppet/ssl/`).
    
3. Transfer the CA cert from the external PostgreSQL instance to the directory you location you just created.

   **Note**: In the examples below <PATH TO EXTERNAL POSTGRESQL CA CERT> is the path to the certificate on your system.
   
4. Ensure that the certificate and directory are owned by the `pe-puppet` user:

   `chown -R pe-puppet:pe-puppet <PATH TO DIRECTORY>`
   
5. Update PE Infrastructure classification to require SSL for database connections:

   a. In the console, click **Nodes** > **Classification**. 
      
   b. On the **Classification** page, select the **PE Infrastructure** group.
   
   c. Click the **Classes** tab. 
   
   d. Find the class `puppet_enterprise`, and from the **Parameter** list, select `database_ssl`.
   
   e. Set the `database_ssl` parameter to "true", and click **Add parameter**.
   
   f. Click the **Commit change** button.
   
6. Update the PE PuppetDB classification to provide the database properties:

   a. In the console, click **Nodes** > **Classification**.
      
   b. On the **Classification** page, select the **PE PuppetDB** group.
   
   c. Click the **Classes** tab.
   
   d. Find the class `puppet_enterprise::profile::puppetdb`, and from the **Parameter** list, select `database_properties`.
   
   e. In the **Value** field, enter: `?ssl=true&sslfactory=org.postgresql.ssl.jdbc4.LibPQFactory&sslmode=verify-full&sslrootcert=<PATH TO EXTERNAL POSTGRESQL CA CERT>`
   
   f. Click **Add parameter**.
   
   g. Click the **Commit change** button.
    
7. Navigate to the `/etc/puppetlabs/console-services/conf.d` directory and update the database configuration files:

   a. Edit `activity-database.conf` so that `subname` has the following setting:
   
   ~~~
"//<POSTGRESQL_SERVER_HOSTNAME>:5432/pe-activity?ssl=true&sslfactory=org.postgresql.ssl.jdbc4.LibPQFactory&sslmode=verify-full&sslrootcert=<PATH TO EXTERNAL POSTGRESQL CA CERT>"
   ~~~
      
   b. Edit `classifier-database.conf` so that `subname` has the following setting:
   
   ~~~
   "//<POSTGRESQL_SERVER_HOSTNAME>:5432/pe-classifier?ssl=true&sslfactory=org.postgresql.ssl.jdbc4.LibPQFactory&sslmode=verify-full&sslrootcert=<PATH TO EXTERNAL POSTGRESQL CA CERT>"
   ~~~
       
   c. Edit `rbac-database.conf` so that `subname` has the following setting:
   
   ~~~
 "//<POSTGRESQL_SERVER_HOSTNAME>:5432/pe-rbac?ssl=true&sslfactory=org.postgresql.ssl.jdbc4.LibPQFactory&sslmode=verify-full&sslrootcert=<PATH TO EXTERNAL POSTGRESQL CA CERT>"
   ~~~
                  
8. On the PE console node (or on the Puppet master in a monolithic install), restart the pe-console-services by running `service pe-console-services restart`.
9. On the PE console and PuppetDB nodes (or on the Puppet master in a monolithic install), start the Puppet agent service by running `puppet resource service puppet ensure=running`.
10. On the PE console node (or on the Puppet master in a monolithic install), kick off a Puppet run. 


