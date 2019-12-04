---
layout: default
title: "Upgrading Puppet Enterprise: Split"
canonical: "/pe/latest/upgrade_split.html"
---

This guide provides upgrade instructions for a split installation. For instructions on upgrading a monolithic installation, refer to [Upgrading Puppet Enterprise: Monolithic](./upgrade_split.html).

## Before beginning: review upgrade information

We support the following upgrade paths:

<table>
     <tr>
       <th>PE version</th>
       <th>Upgrade from</th>
     </tr>
     <tr>
      <td>The latest 2016.4.z release</td>
        <td>
        <p>Any .z release from the following versions:</p>
        <ul>
         <li>2016.4.z</li>
         <li>2016.2.z</li>
         <li>2016.1.z</li>
         <li>2015.3.z</li>
         <li>2015.2.z</li>
        </ul>
       </td>
       </tr>
   </table>

### On PE 3.8?

If you are on PE 3.8, you need to follow the [3.8 to 2016.4 migration documentation](./migrate_monolithic.html), as upgrades from 3.8 are not supported.

In addition, the [Puppet 3 to Puppet 4 upgrade guide](/upgrade/#your-guide-to-modernizing-your-infrastructure) contains useful information about about preparing your Puppet code for migrating from PE 3.8.x to later versions.

### Upgrade overview

The upgrade process consists of the following high-level steps:

1. (For external PostgreSQL users) Prepare your external PostgreSQL instance for upgrade.
2. (Using legacy `auth.conf`?) Prepare your auth.conf for upgrade.
3. Upgrade your monolithic installation.
4. Upgrade any additional PE infrastructure, including compile masters, Puppet agents, ActiveMQ hubs and spokes, and Razor.


### Upgrading an external PostgreSQL instance

These tasks should be done **BEFORE** starting the upgrade.

If you prefer to use an existing instance of PostgreSQL, that instance needs to be manually configured with the correct users and access, and it must be version 9.4.

If you are using an external PostgreSQL instance, please note the following:

1. If you have not already done so, you need to create databases for RBAC, activity service, and the node classifier before upgrading. **If you are upgrading from PE 2015.2.3,** you also need to create the orchestrator database.

2. **If you are upgrading from 2015.2.3 or 2015.3.3,** and have not already done so, you need to enable the citext extension on the RBAC database, and the `pg_trgm` and `pgcrypto` extensions on the PuppetDB database.

[External PostgreSQL option and prep](./sys_req_extsql.html) provides more information about setting up these databases and extensions.

### Changes to `auth.conf` when upgrading

In previous versions of PE, the `auth.conf` file managed access to Puppet’s HTTPS API. This file was located at `/etc/puppetlabs/puppet/auth.conf` on the Puppet master. **This auth.conf file is no longer supported in PE.**

We’ve introduced a new `auth.conf` (`/etc/puppetlabs/puppetserver/conf.d/auth.conf`) that makes use of Puppet Server’s trapper-keeper services.

If you are upgrading or migrating to PE 2016.4, **you must** move to this new `auth.conf` file format. Follow the instructions at [Puppet Server Configuration Files: Migrating to the HOCON auth.conf Format]({{puppetserver}}/config_file_auth_migration.html).

This upgrade disables `puppet_enterprise::master::use_legacy_auth_conf` if you have set it in `hiera.yaml`.

#### Notable changes to auth.conf:

In a previous version of PE, two new endpoints were added to the new auth.conf:

 * /puppet/v3/environment_classes
 * /puppet/v3/static_file_content

These new endpoints are not compatible with legacy `auth.conf` files.

### Console and Hiera configurations transferred on upgrades

If you have configured any settings via the PE console or Hiera, these settings are imported when you upgrade. These settings are stored in `/etc/puppetlabs/enterprise/conf.d/common.conf`. **Do not** edit or add any additional settings to `common.conf`. Instead add any new settings to Hiera or the PE console as you did before.

>**Note:** Settings in `pe.conf` take precedence over those in `common.conf`.

## Upgrading a split installation

Perform the upgrade in the following order.

**Before you begin:** If your master is set to noop mode (`noop = true` in `puppet.conf` on the master), remove or comment out this flag prior to upgrade. Resources aren't updated appropriately if you upgrade in noop mode.

### Step 1: Upgrade the Puppet master

The following steps should be performed on your Puppet master server.

1. If necessary, review [Upgrading an external PostgreSQL instance](#upgrading-an-external-postgresql-instance).
2. [Download](./install_basic.html#downloading-puppet-enterprise) and unpack the PE installation/upgrade tarball. (Run `tar -xf <tarball>`. Note that you need about 1 GB of space in `/tmp` to untar the installer.)
3. From the installer directory, run the installer/upgrader. The steps will vary depending on the path you choose.

   * To have the installer/upgrade open a copy of `pe.conf` for you to edit and upgrade with, run the installer **without the `-c` flag**.

     ~~~
     sudo ./puppet-enterprise-installer
     ~~~

   * To use a `pe.conf` file that already exists, ensure the parameters from step 5 are set, and run the upgrade **with the -c flag** pointed at the `pe.conf` file.

     ~~~
     sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>
     ~~~

     With this option, your previous `pe.conf` is backed up to `/etc/puppetlabs/enterprise/conf.d/timestamp.conf` and a new `pe.conf` is created at `/etc/puppetlabs/enterprise/conf.d/pe.conf`.

4. Set the following parameters:

   >**Important:** Do not use single quotes on parameter values. Use double quotes as shown in the examples.

   >**Note:** In the following examples, `<FQDN>` is the fully qualified domain name of the server. For example, the parameter and value should be expressed as `"puppet_enterprise::puppet_master_host": "master.example.com"`.

   Parameter | Value|
   ---------|-------
   `"console_admin_password":` | `"<PASSWORD>"`— Replace `<PASSWORD>` with your password used to log into the PE console.
   `"puppet_enterprise::puppet_master_host":`| `"<FQDN>"`— Provide the`<FQDN>` of the node hosting the Puppet master.
   `"puppet_enterprise::console_host":` | `"<FQDN>"`— Provide the`<FQDN>` of the node hosting the PE console.
   `"puppet_enterprise::puppetdb_host":` | `"<FQDN>"`— Provide the`<FQDN>` of the node hosting PuppetDB.

5. Save and close the file.

   This starts the upgrade on the Puppet master server.


### Step 2: Upgrade PuppetDB

The following steps should be performed on your PuppetDB server.

1. Transfer the PE installation/upgrade tarball and the `/etc/puppetlabs/enterprise/conf.d/` directory to the PuppetDB node.
2. Unpack the PE installation/upgrade tarball. (Run `tar -xf <tarball>`. Note that you need about 1 GB of space in `/tmp` to untar the installer.)
3. From the installer directory, run the upgrade with `sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>`.

### Step 3: Upgrade PE console

The following steps should be performed on your PE console node.

1. Transfer the PE installation/upgrade tarball and the `/etc/puppetlabs/enterprise/conf.d/` directory to the PE console node.
2. Unpack the PE installation/upgrade tarball. (Run `tar -xf <tarball>`. Note that you need about 1 GB of space in `/tmp` to untar the installer.)
3. From the installer directory, run the upgrade with `sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>`.

### Step 4: Run Puppet on nodes

To complete the upgrade, [run Puppet](./console_classes_groups_running_puppet.html#options-for-running-puppet-on-agent-nodes) on the nodes in the following order:

1. The Puppet master node
2. The PuppetDB node
3. The PE console node

### Step 5: Upgrade your remaining infrastructure

Once the main components of your infrastructure are upgraded (the Puppet master, PuppetDB, and the PE console), you need to upgrade the rest of your infrastructure, as applicable.

1. (**If you have compile masters**) When the upgrade completes, upgrade your compile masters. SSH into each compile master and run the following command:

   ~~~
   /opt/puppetlabs/puppet/bin/curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<PUPPET MASTER FQDN>:8140/packages/current/upgrade.bash | sudo bash
   ~~~

2. (**If you have ActiveMQ hubs and spokes**) Upgrade your ActiveMQ hubs and spokes. SSH into each hub and spoke and run the following command:

   ~~~
   /opt/puppetlabs/puppet/bin/curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<PUPPET MASTER FQDN>:8140/packages/current/upgrade.bash | sudo bash
   ~~~

3. Upgrade these additional PE infrastructure components.

   - [Puppet agents](./install_upgrading_agents.html)
   - [Razor](./razor_upgrade.html)
   - [PE client tools](./install_pe_client_tools.html#matching-client-tools-and-pe-versions)---install the appropriate version of client tools that matches the PE version you've upgraded to.


## Rerunning the upgrade

The PE installer/upgrader is *idempotent*, meaning that you can run it as many times as needed without affecting the outcome. If you encounter any errors during upgrade, you can fix them and run the installer again.

## Upgrade options

As described above, the syntax for the upgrader with a specified `pe.conf` file is:

~~~
sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>
~~~

When you use the `-c` option, the `pe.conf` file name with full path is required. The command will quit with an error message if you omit it.

The following options are also available:

- `-D`: The installer/upgrader will display debugging information.
- `-q`: The installer/upgrader will run in quiet mode. The installation process will not be displayed. If errors occur during the upgrade, the command will quit with an error message.
- `-y`: The installer/upgrader will will run automatically using whatever `pe.conf` is in `/etc/puppetlabs/enterprise/conf.d/`. Note that if this file is incorrect, your upgrade will fail.
- `-V`: The installer/upgrader will display very verbose debugging information.
- `-h`: The installer/upgrader will display help information.

## Checking for updates

[Check here][updateslink] to find out the latest maintenance release of Puppet Enterprise. To see the version of PE you are currently using, run `puppet --version` on the command line.

{% comment %} This link is the same one as the console's help -> version information link. We only have to change the one to update both. {% endcomment %}

[updateslink]: http://info.puppetlabs.com/download-pe.html

**Note: By default, the Puppet master checks for updates whenever the `pe-puppetserver` service restarts.** As part of the check, it passes some basic, anonymous information to Puppet's servers. This behavior can be disabled if need be. See [Disabling update checking](./config_puppetserver.html#disabling-update-checking) for details on what is collected and how to disable checking.




