---
layout: default
title: "External PostgreSQL Option and Prep"
canonical: "/pe/latest/sys_req_extsql.html"
---

There is no requirement to provide a PostgreSQL database, but if you plan to use your own instance of PostgreSQL (as opposed to the instance that PE installs and manages), review the following notes. Your external PostgreSQL instance should be 9.4.

>**Note:** The [`pe.conf` parameter reference](./install_pe_conf_param.html#external-postgresql-parameters) contains required parameters you must add when performing an install or upgrade with an external PostgreSQL instance. 

Refer to [Enabling SSL for PE and external PostgreSQL](./install_ssl_postgresql.html) for additional steps you’ll need to take to ensure SSL is correctly configured between PE and your PostgreSQL database.

1. Create databases for the orchestrator, RBAC, activity service, and the node classifier before installing. The SQL commands to create the databases **resemble** the following:

   ~~~
   CREATE TABLESPACE "pe-orchestrator" LOCATION '/opt/puppetlabs/server/data/postgresql/orchestrator';
   CREATE USER "pe-orchestrator" PASSWORD 'password';
   CREATE DATABASE "pe-orchestrator" OWNER "pe-orchestrator" TABLESPACE "pe-orchestrator" ENCODING 'utf8' LC_CTYPE 'en_US.utf8' LC_COLLATE 'en_US.utf8' template template0;
   
   CREATE TABLESPACE "pe-puppetdb" LOCATION '/opt/puppetlabs/server/data/postgresql/puppetdb';
   CREATE USER "pe-puppetdb" PASSWORD 'password';
   CREATE DATABASE "pe-puppetdb" OWNER "pe-puppetdb" TABLESPACE "pe-puppetdb" ENCODING 'utf8' LC_CTYPE 'en_US.utf8' LC_COLLATE 'en_US.utf8' template template0;

   CREATE TABLESPACE "pe-activity" LOCATION '/opt/puppetlabs/server/data/postgresql/activity';
   CREATE USER "pe-activity" PASSWORD 'password';
   CREATE DATABASE "pe-activity" OWNER "pe-activity" TABLESPACE "pe-activity" ENCODING 'utf8' LC_CTYPE 'en_US.utf8' LC_COLLATE 'en_US.utf8' template template0;

   CREATE TABLESPACE "pe-classifier" LOCATION '/opt/puppetlabs/server/data/postgresql/classifier';
   CREATE USER "pe-classifier" PASSWORD 'password';
   CREATE DATABASE "pe-classifier" OWNER "pe-classifier" TABLESPACE "pe-classifier" ENCODING 'utf8' LC_CTYPE 'en_US.utf8' LC_COLLATE 'en_US.utf8' template template0;

   CREATE TABLESPACE "pe-rbac" LOCATION '/opt/puppetlabs/server/data/postgresql/rbac';
   CREATE USER "pe-rbac" PASSWORD 'password';
   CREATE DATABASE "pe-rbac" OWNER "pe-rbac" TABLESPACE "pe-rbac" ENCODING 'utf8' LC_CTYPE 'en_US.utf8' LC_COLLATE 'en_US.utf8' template template0;
   ~~~

2. Enable the [citext extension](http://www.postgresql.org/docs/9.4/static/citext.html) on the RBAC database. To do so, install the `postgresql-contrib` package, and then from inside the RBAC database, run the following commands:

   ~~~
   sudo -u postgres sh
   psql pe_rbac -c 'create extension citext'
   exit
   ~~~

3. Install the [`pg_trgm` extension](http://www.postgresql.org/docs/9.4/static/pgtrgm.html) on the PuppetDB database. This may require the `postgresql-contrib` (or equivalent) package depending on your distribution. To install the extension, run the following commands:

   ~~~
   sudo -u postgres sh
   psql puppetdb -c 'create extension pg_trgm'
   exit
   ~~~

4. Install the [`pgcrypto` extension](http://www.postgresql.org/docs/9.4/static/pgcrypto.html) on the PuppetDB database. To install the extension, run the following commands:

   ~~~
   sudo -u postgres sh
   psql puppetdb -c 'create extension pgcrypto'
   exit
   ~~~

5. Set your PostgreSQL `max_connections` setting to 200. 

Consult the [PostgreSQL documentation](http://www.postgresql.org/docs/) for more information about configuring your PostgreSQL database.
