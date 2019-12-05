---
layout: default
title: "Enabling SSL for PE and external PostgreSQL"
canonical: "/pe/latest/install_ssl_postgresql.html"
---


If you are using an external PostgreSQL instance instead of the one PE installs, there are some additional steps you'll need to take after installing PE to ensure SSL is correctly configured between PE and your PostgreSQL database.

The process involves:

1. Review the [External PostgreSQL option and prep notes](./install_system_requirements.html#external-postgresql-option-and-prep-notes).  
1. Copy PE SSL files to the PE PostgreSQL certs directory.
2. Update `postgresql.conf`.
3. Depending on the type of SSL authentication you want to use, choose one of the following options:

   - [Option 1: Update PE and external PostgreSQL to use SSL with password authentication](#step-3-option1-update-pe-and-external-postgresql-to-use-ssl-with-password-authentication)
   - [Option 2: Update PE and external PostgreSQL to use SSL with cert-based authentication](#step-3-option-2-update-pe-and-external-postgresql-to-use-ssl-with-cert-based-authentication)
   
>**Note:** Your external PostgreSQL instance should be 9.4.
   
### Step 1: Copy PE SSL files to the PE PostgreSQL certs directory

>**Note**: The following examples use `$PGDATA` for the PostgreSQL config directory. `$PGDATA` is typically `/opt/puppetlabs/server/data/postgresql/9.4/data/`.

1. Create a `certs` directory in `$PGDATA`.
2. Copy the the PE SSL cert and private key to the PostgreSQL `certs` directory.

   a. Copy `/etc/puppetlabs/puppet/ssl/certs/<POSTGRESQL HOSTNAME>.pem` to `$PGDATA/certs/<POSTGRESQL HOSTNAME>.cert.pem`.

   b. Copy `/etc/puppetlabs/puppet/ssl/private_keys/<POSTGRESQL HOSTNAME>.pem` to `$PGDATA/certs/<POSTGRESQL HOSTNAME>.private_key.pem`.

3. Ensure these files are owned by the Postgres user and the file permissions are only readable by that user. From `$PGDATA/certs/`, run the following commands:

   ~~~
   chmod 400 *.pem
   chown postgres:postgres *.pem
   ~~~


### Step 2: Update `postgresql.conf`

1. Navigate to `$PGDATA` and edit `postgresql.conf` with the following values:

   a. `ssl = on`

   b. `ssl_cert_file = 'certs/<POSTGRESQL HOSTNAME>.cert.pem'`

   c. `ssl_key_file = 'certs/<POSTGRESQL HOSTNAME>.private_key.pem'`

   d. `ssl_ca_file = '/etc/puppetlabs/puppet/ssl/certs/ca.pem'`

2. Make sure the file has no entry resembling `ssl = off`.
3. Save and exit the file.
4. Restart PostgreSQL.

If there are any errors check your postgresql log file. Potential errors that might happen include certs/keys not being readable by PostgreSQL.

>**Note**: If you are managing PostgreSQL with Puppet, make sure to add these SSL settings in your Puppet manifest instead of in `postgresql.conf`; otherwise, Puppet will remove the entries the next time it runs.

### Step 3, Option 1: Update PE and external PostgreSQL to use SSL with password authentication

1. From the console, click __Nodes__ > __Classification__.
2. From the __Classification page__, select the __PE Infrastructure__ group.
3. Click the __Classes__ tab.
4. Find the class `puppet_enterprise`, and edit the `database_ssl` parameter to `true`.
5. Click the **Commit change** button.
6. Generate .pk8 files for the PuppetDB, console-services, and orchestration-services databases. Run the following commands:

   ~~~
   openssl pkcs8 -topk8 -inform PEM -outform DER -in /etc/puppetlabs/puppetdb/ssl/<CERTNAME>.private_key.pem -out /etc/puppetlabs/puppetdb/ssl/<CERTNAME>.private_key.pk8 -nocrypt
   chown -R pe-puppetdb:pe-puppetdb /etc/puppetlabs/puppetdb/ssl
   
   openssl pkcs8 -topk8 -inform PEM -outform DER -in /etc/puppetlabs/orchestration-services/ssl/<CERTNAME>.private_key.pem -out /etc/puppetlabs/orchestration-services/ssl/<CERTNAME>.private_key.pk8 -nocrypt
 chown -R pe-orchestration-services:pe-orchestration-services /etc/puppetlabs/orchestration-services/ssl/
 
   openssl pkcs8 -topk8 -inform PEM -outform DER -in /opt/puppetlabs/server/data/console-services/certs/<CERTNAME>.private_key.pem -out /opt/puppetlabs/server/data/console-services/certs/<CERTNAME>.private_key.pk8 -nocrypt
 chown -R pe-console-services:pe-console-services /opt/puppetlabs/server/data/console-services/certs/
   ~~~

7. Restart the PuppetDB service, the console-services, and the orchestration-service. Run the following commands:

   ~~~
   service pe-console-services restart
   service pe-puppetdb restart
   service pe-orchestration-services restart
   ~~~
   
8. Navigate to `/etc/puppetlabs/orchestration-services/conf.d/` and edit `orchestrator.conf` so that `subname` has the following setting: 

   ~~~
   "//<POSTGRESQL_SERVER_HOSTNAME>:5432/pe-orchestrator?ssl=true&sslfactory=org.postgresql.ssl.jdbc4.LibPQFactory&sslmode=verify-full&sslrootcert=/etc/puppetlabs/puppet/ssl/certs/ca.pem"     
   ~~~

9. Navigate to `/etc/puppetlabs/puppetdb/conf.d/` and edit `database.ini` so that `subname` has the following setting: 

   ~~~
   "//<POSTGRESQL_SERVER_HOSTNAME>:5432/pe-puppetdb?ssl=true&sslfactory=org.postgresql.ssl.jdbc4.LibPQFactory&sslmode=verify-full&sslrootcert=/etc/puppetlabs/puppet/ssl/certs/ca.pem"     
   ~~~ 

10. Navigate to `/etc/puppetlabs/console-services/conf.d/` and edit `activity-database.conf` so that `subname` has the following setting:

    ~~~
    "//<POSTGRESQL_SERVER_HOSTNAME>:5432/pe-activity?ssl=true&sslfactory=org.postgresql.ssl.jdbc4.LibPQFactory&sslmode=verify-full&sslrootcert=/etc/puppetlabs/puppet/ssl/certs/ca.pem" 
    ~~~
   
11. Navigate to `/etc/puppetlabs/console-services/conf.d/` and edit `classifier-database.conf` so that `subname` has the following setting:

    ~~~
    "//<POSTGRESQL_SERVER_HOSTNAME>:5432/pe-classifier?ssl=true&sslfactory=org.postgresql.ssl.jdbc4.LibPQFactory&sslmode=verify-full&sslrootcert=/etc/puppetlabs/puppet/ssl/certs/ca.pem"
    ~~~
   
12. Navigate to `/etc/puppetlabs/console-services/conf.d/` and edit `rbac-database.conf` so that `subname` has the following setting:

    ~~~
    "//<POSTGRESQL_SERVER_HOSTNAME>:5432/pe-rbac?ssl=true&sslfactory=org.postgresql.ssl.jdbc4.LibPQFactory&sslmode=verify-full&sslrootcert=/etc/puppetlabs/puppet/ssl/certs/ca.pem"
    ~~~
  
13. Kick off a Puppet run on the Puppet master (for a monolithic install), or on the Puppet master, PE console, and PuppetDB nodes (in a split install). 



### Step 3, Option 2: Update PE and external PostgreSQL to use SSL with cert-based authentication

1. From the console, click __Nodes__ > __Classification__.
2. From the __Classification page__, select the __PE Infrastructure__ group.
3. Click the __Classes__ tab.
4. Find the class `puppet_enterprise`, and edit the `database_ssl` parameter and `database_cert_auth` parameter to `true`.
5. Click the **Commit change** button.
6. **On the external PostgreSQL server**, navigate to `pg_hba.conf` to allow cert-based authentication for the PE databases. The entry should resemble the following:


   ~~~PostgreSQL
   # Rule Name: database cert auth rule
   # Description: none
   # Order: 0
     hostssl pe-puppetdb pe-puppetdb 0.0.0.0/0 cert  map=pe-puppetdb-map
   # Rule Name: database ipv6 cert auth rule
   # Description: none
   # Order: 1
     hostssl pe-puppetdb pe-puppetdb ::/0  cert  map=pe-puppetdb-map
     
   # Rule Name: database cert auth rule
   # Description: none
   # Order: 0
     hostssl pe-console-services pe-console-services 0.0.0.0/0 cert  map=pe-console-services-map
   # Rule Name: database ipv6 cert auth rule
   # Description: none
   # Order: 1
     hostssl pe-console-services pe-console-services ::/0  cert  map=pe-console-services-map
     
   # Rule Name: database cert auth rule
   # Description: none
   # Order: 0
     hostssl pe-orchestration-services pe-orchestration-services 0.0.0.0/0 cert  map=pe-orchestration-services-map
   # Rule Name: database ipv6 cert auth rule
   # Description: none
   # Order: 1
     hostssl pe-orchestration-services pe-orchestration-services ::/0  cert  map=pe-orchestration-services-map
   ~~~

7. **On the external PostgreSQL server**, create a `pg_ident.conf` file that lists which SSL cert is allowed access to which database. Your entry should resemble the following:

   ~~~
   pe-puppetdb-map <HOSTNAME OF SERVER HOSTING PUPPETDB> pe-puppetdb
   pe-orchestration-services-map <HOSTNAME OF SERVER HOSTING PUPPET MASTER> pe-orchestration-services
   pe-console-services-map <HOSTNAME OF SEVER HOSTING PE CONSOLE> pe-console-services
   ~~~

8. **On the external PostgreSQL server**, restart the PostgreSQL service.

9. Generate .pk8 files for the PuppetDB, the console-services, and the orchestration-services databases. Run the following commands:

   ~~~
   openssl pkcs8 -topk8 -inform PEM -outform DER -in /etc/puppetlabs/puppetdb/ssl/<CERTNAME>.private_key.pem -out /etc/puppetlabs/puppetdb/ssl/<CERTNAME>.private_key.pk8 -nocrypt
   chown -R pe-puppetdb:pe-puppetdb /etc/puppetlabs/puppetdb/ssl
   
   openssl pkcs8 -topk8 -inform PEM -outform DER -in /etc/puppetlabs/orchestration-services/ssl/<CERTNAME>.private_key.pem -out /etc/puppetlabs/orchestration-services/ssl/<CERTNAME>.private_key.pk8 -nocrypt
 chown -R pe-orchestration-services:pe-orchestration-services /etc/puppetlabs/orchestration-services/ssl/
 
   openssl pkcs8 -topk8 -inform PEM -outform DER -in /opt/puppetlabs/server/data/console-services/certs/<CERTNAME>.private_key.pem -out /opt/puppetlabs/server/data/console-services/certs/<CERTNAME>.private_key.pk8 -nocrypt
 chown -R pe-console-services:pe-console-services /opt/puppetlabs/server/data/console-services/certs/
   ~~~

10. Restart the PuppetDB service, the console-services, and the orchestration-service. Run the following commands:

    ~~~
    service pe-console-services restart
    service pe-puppetdb restart
    service pe-orchestration-services restart
    ~~~
   
11. Navigate to `/etc/puppetlabs/orchestration-services/conf.d/` and edit `orchestrator.conf` so that `subname` has the following setting: 

    ~~~
    "//<POSTGRESQL_SERVER_HOSTNAME>:5432/pe-orchestrator?ssl=true&sslfactory=org.postgresql.ssl.jdbc4.LibPQFactory&sslmode=verify-full&sslrootcert=/etc/puppetlabs/puppet/ssl/certs/ca.pem"`      
    ~~~

12. Navigate to `/etc/puppetlabs/puppetdb/conf.d/` and edit `database.ini` so that `subname` has the following setting: 

    ~~~
    "//<POSTGRESQL_SERVER_HOSTNAME>:5432/pe-puppetdb?ssl=true&sslfactory=org.postgresql.ssl.jdbc4.LibPQFactory&sslmode=verify-full&sslrootcert=/etc/puppetlabs/puppet/ssl/certs/ca.pem"     
    ~~~ 

13. Navigate to `/etc/puppetlabs/console-services/conf.d/` and edit `activity-database.conf` so that `subname` has the following setting:

    ~~~
    "//<POSTGRESQL_SERVER_HOSTNAME>:5432/pe-activity?ssl=true&sslfactory=org.postgresql.ssl.jdbc4.LibPQFactory&sslmode=verify-full&sslrootcert=/etc/puppetlabs/puppet/ssl/certs/ca.pem"         
    ~~~
   
14. Navigate to `/etc/puppetlabs/console-services/conf.d/` and edit `classifier-database.conf` so that `subname` has the following setting:

    ~~~
    "//<POSTGRESQL_SERVER_HOSTNAME>:5432/pe-classifier?ssl=true&sslfactory=org.postgresql.ssl.jdbc4.LibPQFactory&sslmode=verify-full&sslrootcert=/etc/puppetlabs/puppet/ssl/certs/ca.pem"
    ~~~
   
15. Navigate to `/etc/puppetlabs/console-services/conf.d/` and edit `rbac-database.conf` so that `subname` has the following setting:

    ~~~
    "//<POSTGRESQL_SERVER_HOSTNAME>:5432/pe-rbac?ssl=true&sslfactory=org.postgresql.ssl.jdbc4.LibPQFactory&sslmode=verify-full&sslrootcert=/etc/puppetlabs/puppet/ssl/certs/ca.pem"
    ~~~
  
16. Kick off a Puppet run on the Puppet master (for a monolithic install), or on the Puppet master, PE console, and PuppetDB nodes (in a split install). 