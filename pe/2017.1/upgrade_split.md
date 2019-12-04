---
layout: default
title: "Upgrading Puppet Enterprise: Split and LEI"
canonical: "/pe/latest/upgrade_split.html"
---

You can upgrade your split PE (and LEI) infrastructure as new releases become available.

## Supported upgrade paths

We support the following upgrade paths. If you do not see your version listed, you may first need to [upgrade to a  version on the 2016.4 line](/pe/2016.4/upgrade_mono.html), which is our [long-term support release](./overview_getting_support.html#puppet-enterprise-support-life-cycle).

   <table>
     <tr>
       <th>PE version</th>
       <th>Upgrade from</th>
     </tr>
     <tr>
      <td>2017.1.1</td>
        <td>Any .z release from the following versions:
        <ul>
         <li>2017.1.z</li>
         <li>2016.5.z</li>
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

   If you are on PE 3.8, follow the [3.8 to 2017.1 migration documentation](./migrate_monolithic.html), as upgrades from 3.8 are not supported. In addition, the [Puppet 3 to Puppet 4 upgrade guide](/upgrade/#your-guide-to-modernizing-your-infrastructure) contains useful information about about preparing your Puppet code for migrating from PE 3.8.x to later versions.

## Upgrade PE: split installation

When you upgrade a split (and LEI) installation, the main components of PE (the Puppet master, PuppetDB, the PE console, and any compile masters/hubs and spokes) are upgraded on the different nodes they are installed on.

The PE installer is *idempotent*, meaning that you can run it as many times as needed without affecting the outcome. If you encounter errors during installation, you can fix them and run the installer again.

The upgrade process for a split installation consists of the following high-level steps:

1. Upgrade your Puppet master, PuppetDB, and PE console nodes.
2. Upgrade your remaining infrastructure and LEI nodes
3. Upgrade your Puppet agents.

**Before you begin:** If your master is set to noop mode (`noop = true` in `puppet.conf` on the master), remove or comment out this flag prior to upgrade. The installer fails if you attempt to upgrade in noop mode.

### Upgrade the Puppet master server

When you upgrade a split installation, you first upgrade the Puppet master.

1. [Download](./install_basic.html#downloading-puppet-enterprise) and unpack the PE installation/upgrade tarball. (Run `tar -xf <tarball>`. Note that you need about 1 GB of space in `/tmp` to untar the installer.)
2. Stop the Puppet agent service with `sudo puppet resource service puppet ensure=stopped`.
3. From the installer directory, run the installer/upgrader. The steps will vary depending on the path you choose.

   * To use a `pe.conf` file that already exists run the upgrade **with the -c flag** pointed at the `pe.conf` file.

     ~~~
     sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>
     ~~~

     With this option, your previous `pe.conf` is backed up to `/etc/puppetlabs/enterprise/conf.d/timestamp.conf` and a new `pe.conf` is created at `/etc/puppetlabs/enterprise/conf.d/pe.conf`.

     >**Warning:** If you're using a pre-populated file, be sure you've set the parameters correctly as detailed in the following steps.

   * To have the installer/upgrade open a copy of `pe.conf` for you to edit and upgrade with, run the installer **without the `-c` flag**.

     ~~~
     sudo ./puppet-enterprise-installer
     ~~~

4. Set the following parameters in `pe.conf`:

   >**Important:** Do not use single quotes on parameter values. Use double quotes as shown in the examples.

   >**Note:** In the following examples, `<FQDN>` is the fully qualified domain name of the server. For example, the parameter and value should be expressed as `"puppet_enterprise::puppet_master_host": "master.example.com"`.

   Parameter | Value|
   ---------|-------
   `"console_admin_password":` | `"<PASSWORD>"`— Replace `<PASSWORD>` with your password used to log into the PE console.
   `"puppet_enterprise::puppet_master_host":`| `"<FQDN>"`— Provide the`<FQDN>` of the node hosting the Puppet master.
   `"puppet_enterprise::console_host":` | `"<FQDN>"`— Provide the`<FQDN>` of the node hosting the PE console.
   `"puppet_enterprise::puppetdb_host":` | `"<FQDN>"`— Provide the`<FQDN>` of the node hosting PuppetDB.

5. **If you're upgrading an installation that uses an external PostgreSQL instance,** set the following parameters in `pe.conf`:

   >**Important:** If needed, substitute the database user names if you've replaced the defaults, which are shown in this step.

   Parameter | Value|
   ---------|--------
   `"puppet_enterprise::activity_service_regular_db_user":` | `"pe-activity"`
   `"puppet_enterprise::activity_service_migration_db_user":` | `"pe-activity"`
   `"puppet_enterprise::classifier_service_regular_db_user":` | `"pe-classifier"`
   `"puppet_enterprise::classifier_service_migration_db_user":` | `"pe-classifier"`
   `"puppet_enterprise::orchestrator_service_regular_db_user":` | `"pe-orchestrator"`
   `"puppet_enterprise::orchestrator_service_migration_db_user":` | `"pe-orchestrator"`
   `"puppet_enterprise::rbac_service_regular_db_user":` | `"pe-rbac"`
   `"puppet_enterprise::rbac_service_migration_db_user":` | `"pe-rbac"`


6. Save and close the file.

   This starts the upgrade on the Puppet master server.

### Upgrade the PuppetDB server

In a split installation, after you upgrade the Puppet master, you're ready to upgrade your PuppetDB server.

1. Transfer the PE installation/upgrade tarball and the `/etc/puppetlabs/enterprise/conf.d/` directory to the PuppetDB node.
2. Unpack the PE installation/upgrade tarball. (Run `tar -xf <tarball>`. Note that you need about 1 GB of space in `/tmp` to untar the installer.)
3. Stop the Puppet agent service with `sudo puppet resource service puppet ensure=stopped`.
4. From the installer directory, run the upgrade with `sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>`.

### Upgrade the PE console server

In a split installation, after you upgrade PuppetDB, you're ready to upgrade your PE console server.

1. Transfer the PE installation/upgrade tarball and the `/etc/puppetlabs/enterprise/conf.d/` directory to the PE console node.
2. Unpack the PE installation/upgrade tarball. (Run `tar -xf <tarball>`. Note that you need about 1 GB of space in `/tmp` to untar the installer.)
3. Stop the Puppet agent service with `sudo puppet resource service puppet ensure=stopped`.
4. From the installer directory, run the upgrade with `sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>`.

### Run Puppet on the infrastructure nodes

To complete the upgrade of your core infrastructure nodes, you need to run Puppet on all three servers.

Run Puppet in the order specified.

1. The Puppet master node
2. The PuppetDB node
3. The PE console node

### Upgrade your remaining infrastructure and LEI nodes

Once the main components of your infrastructure are upgraded (the Puppet master, PuppetDB, and the PE console), you need to upgrade the rest of your infrastructure, including any LEI nodes, as applicable.

1. (**If you have compile masters**) Upgrade your compile masters. SSH into each compile master and run the following command:

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

## Upgrade options

When you run the upgrade, you can pass additional flags to control the upgrade.

As described above, the syntax for the upgrader with a specified `pe.conf` file is:

~~~
sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>
~~~

When you use the `-c` option, the `pe.conf` file name with full path is required. The command will quit with an error message if you omit it.

When you use the `-c` option, you can also use the following options:

Option          | Definition  |
----------------|---------------|
`-D` | The installer will display debugging information |
`-q` | The installer will run in quiet mode. The installation process will not be displayed. If errors occur during the installation, the command will quit with an error message. |
`-y` | The installer/upgrader will will run automatically using whatever `pe.conf` is in `/etc/puppetlabs/enterprise/conf.d/`. Note that if this file is incorrect, your upgrade will fail.
`-V` | The installer will display very verbose debugging information. |
`-h` | The installer will display help information.


## Checking for updates

[Check here][updateslink] to find out the latest maintenance release of Puppet Enterprise. To see the version of PE you are currently using, run `puppet --version` on the command line.

{% comment %} This link is the same one as the console's help -> version information link. We only have to change the one to update both. {% endcomment %}

[updateslink]: http://info.puppetlabs.com/download-pe.html

**Note: By default, the Puppet master checks for updates whenever the `pe-puppetserver` service restarts.** As part of the check, it passes some basic, anonymous information to Puppet's servers. This behavior can be disabled if need be. See [Disabling update checking](./config_puppetserver.html#disabling-update-checking) for details on what is collected and how to disable checking.