---
layout: default
title: "Enabling SSL for PE and external PostgreSQL"
canonical: "/pe/latest/install_ssl_postgresql.html"
---


> **WARNING**: The procedure in this document has been created and verified by Puppet's professional services engineers. It has not gone through the usual quality assurance process. Before undertaking any of these procedures, we strongly advise that you review them thoroughly and test them in non-production environments. Beyond validation from our professional services engineers, Puppet does not offer support for this procedure outside of a Professional Services engagement.


You can use an external PostgreSQL instance instead of the one PE installs, but you'll need some additional configuration after to ensure SSL is correctly configured between PE and your PostgreSQL database.

## Enable PE SSL for external PostgreSQL

After you install PE, you need additional configuration to establish SSL authentication between PE and your PostgreSQL instance.

Before you begin:

1. Review the [External PostgreSQL option and prep notes](./sys_req_extsql.html).
2. [Install PE](./install_basic.html).
3. Ensure your external PostgreSQL instance is 9.4 or greater.

Perform the following tasks in the order given:

1. Prepare the PE PostgreSQL certs directory and update `postgresql.conf`
2. Establish SSL between PE and the external PostgreSQL instance. Depending on the type of SSL authentication you want to use, choose one of the following options:

   - Establish SSL with password authentication
   - Establish SSL with certificate-based authentication

### Prepare the PE PostgreSQL certs directory and update `postgresql.conf`

Before you can establish SSL authentication between PE and PostgreSQL, you must perform some initial configuration.

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
   
4. In `$PGDATA`, edit `postgresql.conf` so that it contains the following values:

   >**Note**: If you are managing PostgreSQL with Puppet, make sure to add these SSL settings in your Puppet manifest instead of in `postgresql.conf`; otherwise, Puppet will remove the entries the next time it runs.

   a. `ssl = on`

   b. `ssl_cert_file = 'certs/<POSTGRESQL HOSTNAME>.cert.pem'`

   c. `ssl_key_file = 'certs/<POSTGRESQL HOSTNAME>.private_key.pem'`

   d. `ssl_ca_file = '/etc/puppetlabs/puppet/ssl/certs/ca.pem'`

5. Make sure the file has no entry resembling `ssl = off`.
6. Save and exit the file, and then restart your PostgreSQL instance.

If there are any errors check your PostgreSQL log file. Potential errors that might happen include certs/keys not being readable by PostgreSQL.


### Establish SSL with password authentication

Once you've prepared the PE PostgreSQL directory, you can establish SSL connections between PE and PostgreSQL with password authentication.

1. In the console, click **Classification**, and select the **PE Infrastructure** node group.
2. On the **Classes** tab, find the class `puppet_enterprise`, and edit the `database_ssl` parameter to `true`.
3. Commit changes.
4. Generate .pk8 files for the PuppetDB, console-services, and orchestration-services databases. Run the following commands:

   ~~~
   openssl pkcs8 -topk8 -inform PEM -outform DER -in /etc/puppetlabs/puppetdb/ssl/<CERTNAME OF PUPPETDB NODE>.private_key.pem -out /etc/puppetlabs/puppetdb/ssl/<CERTNAME OF PUPPETDB NODE>.private_key.pk8 -nocrypt
   chown -R pe-puppetdb:pe-puppetdb /etc/puppetlabs/puppetdb/ssl

   openssl pkcs8 -topk8 -inform PEM -outform DER -in /etc/puppetlabs/orchestration-services/ssl/<CERTNAME OF PUPPETDB NODE>.private_key.pem -out /etc/puppetlabs/orchestration-services/ssl/<CERTNAME OF PUPPETDB NODE>.private_key.pk8 -nocrypt
   chown -R pe-orchestration-services:pe-orchestration-services /etc/puppetlabs/orchestration-services/ssl/

   openssl pkcs8 -topk8 -inform PEM -outform DER -in /opt/puppetlabs/server/data/console-services/certs/<CERTNAME OF CONSOLE NODE>.private_key.pem -out /opt/puppetlabs/server/data/console-services/certs/<CERTNAME OF CONSOLE NODE>.private_key.pk8 -nocrypt
   chown -R pe-console-services:pe-console-services /opt/puppetlabs/server/data/console-services/certs/
   ~~~

5. Reload the PuppetDB service, the console-services, and the orchestration-services. Run the following commands:

   ~~~
   service pe-console-services reload
   service pe-puppetdb reload
   service pe-orchestration-services reload
   ~~~

6. Navigate to `/etc/puppetlabs/orchestration-services/conf.d/` and edit `orchestrator.conf` so that `subname` has the following setting:

   ~~~
   "//<POSTGRESQL_SERVER_HOSTNAME>:5432/pe-orchestrator?ssl=true&sslfactory=org.postgresql.ssl.jdbc4.LibPQFactory&sslmode=verify-full&sslrootcert=/etc/puppetlabs/puppet/ssl/certs/ca.pem"
   ~~~

7. Navigate to `/etc/puppetlabs/puppetdb/conf.d/` and edit `database.ini` so that `subname` has the following setting:

   ~~~
   "//<POSTGRESQL_SERVER_HOSTNAME>:5432/pe-puppetdb?ssl=true&sslfactory=org.postgresql.ssl.jdbc4.LibPQFactory&sslmode=verify-full&sslrootcert=/etc/puppetlabs/puppet/ssl/certs/ca.pem"
   ~~~

8. Navigate to `/etc/puppetlabs/console-services/conf.d/` and edit `activity-database.conf` so that `subname` has the following setting:

   ~~~         
      "//<POSTGRESQL_SERVER_HOSTNAME>:5432/pe-activity?ssl=true&sslfactory=org.postgresql.ssl.jdbc4.LibPQFactory&sslmode=verify-full&sslrootcert=/etc/puppetlabs/puppet/ssl/certs/ca.pem"
   ~~~

9. Navigate to `/etc/puppetlabs/console-services/conf.d/` and edit `classifier-database.conf` so that `subname` has the following setting:

   ~~~
   "//<POSTGRESQL_SERVER_HOSTNAME>:5432/pe-classifier?ssl=true&sslfactory=org.postgresql.ssl.jdbc4.LibPQFactory&sslmode=verify-full&sslrootcert=/etc/puppetlabs/puppet/ssl/certs/ca.pem"
   ~~~

10. Navigate to `/etc/puppetlabs/console-services/conf.d/` and edit `rbac-database.conf` so that `subname` has the following setting:

    ~~~
    "//<POSTGRESQL_SERVER_HOSTNAME>:5432/pe-rbac?ssl=true&sslfactory=org.postgresql.ssl.jdbc4.LibPQFactory&sslmode=verify-full&sslrootcert=/etc/puppetlabs/puppet/ssl/certs/ca.pem"
    ~~~

11. Kick off a Puppet run on the Puppet master (for a monolithic install), or on the Puppet master, PE console, and PuppetDB nodes (in a split install).



### Establish SSL with certificate-based authentication

Once you've prepared the PE PostgreSQL directory, you can establish SSL connections between PE and PostgreSQL with certificate-based authentication.

1. In the console, click **Classification**, and select the **PE Infrastructure** node group.
2. On the **Classes** tab, find the class `puppet_enterprise`, and edit the `database_ssl` parameter and `database_cert_auth` parameter to `true`.
3. Commit changes.
4. **On the external PostgreSQL server**, navigate to `pg_hba.conf` to allow cert-based authentication for the PE databases. The entry should resemble the following:


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

5. **On the external PostgreSQL server**, create a `pg_ident.conf` file that lists which SSL cert is allowed access to which database. Your entry should resemble the following:

   ~~~
   pe-puppetdb-map <HOSTNAME OF SERVER HOSTING PUPPETDB> pe-puppetdb
   pe-orchestration-services-map <HOSTNAME OF SERVER HOSTING PUPPET MASTER> pe-orchestration-services
   pe-console-services-map <HOSTNAME OF SEVER HOSTING PE CONSOLE> pe-console-services
   ~~~

6. **On the external PostgreSQL server**, restart the PostgreSQL service.

7. Generate .pk8 files for the PuppetDB, the console-services, and the orchestration-services databases. Run the following commands:

   ~~~
   openssl pkcs8 -topk8 -inform PEM -outform DER -in /etc/puppetlabs/puppetdb/ssl/<CERTNAME OF PUPPETDB NODE>.private_key.pem -out /etc/puppetlabs/puppetdb/ssl/<CERTNAME OF PUPPETDB NODE>.private_key.pk8 -nocrypt
   chown -R pe-puppetdb:pe-puppetdb /etc/puppetlabs/puppetdb/ssl

   openssl pkcs8 -topk8 -inform PEM -outform DER -in /etc/puppetlabs/orchestration-services/ssl/<CERTNAME OF PUPPET MASTER NODE>.private_key.pem -out /etc/puppetlabs/orchestration-services/ssl/<CERTNAME OF PUPPET MASTER NODE>.private_key.pk8 -nocrypt
 chown -R pe-orchestration-services:pe-orchestration-services /etc/puppetlabs/orchestration-services/ssl/

   openssl pkcs8 -topk8 -inform PEM -outform DER -in /opt/puppetlabs/server/data/console-services/certs/<CERTNAME OF CONSOLE NODE>.private_key.pem -out /opt/puppetlabs/server/data/console-services/certs/<CERTNAME OF CONSOLE NODE>.private_key.pk8 -nocrypt
 chown -R pe-console-services:pe-console-services /opt/puppetlabs/server/data/console-services/certs/
   ~~~

8. Reload the PuppetDB service, the console-services, and the orchestration-service. Run the following commands:

   ~~~
   service pe-console-services reload
   service pe-puppetdb reload
   service pe-orchestration-services reload
   ~~~

9. Navigate to `/etc/puppetlabs/orchestration-services/conf.d/` and edit `orchestrator.conf` so that `subname` has the following setting:

   ~~~
    "//<POSTGRESQL_SERVER_HOSTNAME>:5432/pe-orchestrator?ssl=true&sslfactory=org.postgresql.ssl.jdbc4.LibPQFactory&sslmode=verify-full&sslrootcert=/etc/puppetlabs/puppet/ssl/certs/ca.pem"`
   ~~~

10. Navigate to `/etc/puppetlabs/puppetdb/conf.d/` and edit `database.ini` so that `subname` has the following setting:

    ~~~
    "//<POSTGRESQL_SERVER_HOSTNAME>:5432/pe-puppetdb?ssl=true&sslfactory=org.postgresql.ssl.jdbc4.LibPQFactory&sslmode=verify-full&sslrootcert=/etc/puppetlabs/puppet/ssl/certs/ca.pem"
    ~~~

11. Navigate to `/etc/puppetlabs/console-services/conf.d/` and edit `activity-database.conf` so that `subname` has the following setting:

    ~~~
    "//<POSTGRESQL_SERVER_HOSTNAME>:5432/pe-activity?ssl=true&sslfactory=org.postgresql.ssl.jdbc4.LibPQFactory&sslmode=verify-full&sslrootcert=/etc/puppetlabs/puppet/ssl/certs/ca.pem"
    ~~~

12. Navigate to `/etc/puppetlabs/console-services/conf.d/` and edit `classifier-database.conf` so that `subname` has the following setting:

    ~~~
    "//<POSTGRESQL_SERVER_HOSTNAME>:5432/pe-classifier?ssl=true&sslfactory=org.postgresql.ssl.jdbc4.LibPQFactory&sslmode=verify-full&sslrootcert=/etc/puppetlabs/puppet/ssl/certs/ca.pem"
    ~~~

13. Navigate to `/etc/puppetlabs/console-services/conf.d/` and edit `rbac-database.conf` so that `subname` has the following setting:

    ~~~
    "//<POSTGRESQL_SERVER_HOSTNAME>:5432/pe-rbac?ssl=true&sslfactory=org.postgresql.ssl.jdbc4.LibPQFactory&sslmode=verify-full&sslrootcert=/etc/puppetlabs/puppet/ssl/certs/ca.pem"
    ~~~

14. Kick off a Puppet run on the Puppet master (for a monolithic install), or on the Puppet master, PE console, and PuppetDB nodes (in a split install).