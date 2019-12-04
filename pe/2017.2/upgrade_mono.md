---
layout: default
title: "Upgrading Puppet Enterprise: Monolithic"
canonical: "/pe/latest/upgrade_mono.html"
---

You can upgrade your monolithic PE infrastructure as new releases become available.

## Supported upgrade paths

We support the following upgrade paths. Note that the 2016.4 line is our [long-term support release line](./overview_getting_support.html#puppet-enterprise-support-life-cycle).

   <table>
     <tr>
       <th>PE version</th>
       <th>Upgrade from</th>
     </tr>
     <tr>
      <td>2017.2.3</td>
        <td>Any .z release from the following versions:
        <ul>
         <li>2017.1.z</li>
         <li>2016.5.z</li>
         <li>2016.4.z</li>
        </ul>
       </td>
       </tr>
   </table>

### On PE 3.8?

   If you are on PE 3.8, follow the [3.8 to 2017.2 migration documentation](./migrate_monolithic.html), as upgrades from 3.8 are not supported. In addition, the [Puppet 3 to Puppet 4 upgrade guide](/upgrade/#your-guide-to-modernizing-your-infrastructure) contains useful information about about preparing your Puppet code for migrating from PE 3.8.x to later versions.


## Upgrade a monolithic installation

When you upgrade a monolithic installation, the main components of PE (the Puppet master, PuppetDB, and the PE console) are upgraded on the same node.

If you encounter errors during upgrade, you can fix them and run the installer again.

**Before you begin:** If your master is set to noop mode (`noop = true` in `puppet.conf` on the master), remove or comment out this flag prior to upgrade. The installer fails if you attempt to upgrade in noop mode.

1. [Download](./install_basic.html#downloading-puppet-enterprise) and unpack the PE installation tarball: `tar -xf <tarball>`

   You need about 1 GB of space to untar the installer.

2. From the installer directory, run the installer.

     ```
     sudo ./puppet-enterprise-installer
     ```

   **Tip:** To customize configuration by specifying a different `pe.conf` file other than the existing file, use the -c flag:

     ```
     sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>
     ```

     Your previous `pe.conf` is backed up to `/etc/puppetlabs/enterprise/conf.d/pe-<TIMESTAMP>.conf` and a new `pe.conf` is created at `/etc/puppetlabs/enterprise/conf.d/pe.conf`.

3. If you have compile masters, upgrade your compile masters.

   SSH into each compile master and run:

   ```
   /opt/puppetlabs/puppet/bin/curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<PUPPET MASTER FQDN>:8140/packages/current/upgrade.bash | sudo bash
   ```

4. If you have ActiveMQ hubs and spokes, upgrade your ActiveMQ hubs and spokes.

   SSH into each hub and spoke and run:

   ```
   /opt/puppetlabs/puppet/bin/curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<PUPPET MASTER FQDN>:8140/packages/current/upgrade.bash | sudo bash
   ```

5. Upgrade these additional PE infrastructure components.

   - [Puppet agents](./install_upgrading_agents.html)
   - [Razor](./razor_upgrade.html)
   - [PE client tools](./install_pe_client_tools.html#matching-client-tools-and-pe-versions)---install the appropriate version of client tools that matches the PE version you've upgraded to.


## Upgrade a high availability installation

To upgrade a high availability installation, you first upgrade the primary master, and then you upgrade the primary master replica.

You can upgrade from any PE version with high availability support, which began in PE 2016.5, to any subsequent version with HA.

If you encounter errors during upgrade, you can fix them and run the installer again.

**Before you begin:** If your master is set to noop mode (`noop = true` in `puppet.conf` on the master), remove or comment out this flag prior to upgrade. The installer fails if you attempt to upgrade in noop mode.

1. On the primary master replica, stop these services:

   * `pe-puppetdb`
   * `pe-puppetserver`
   * `pe-console-services`

2. [Download](./install_basic.html#downloading-puppet-enterprise) and unpack the PE installation tarball: `tar -xf <tarball>`

   You need about 1 GB of space to untar the installer.

3. From the installer directory, run the installer.

     ```
     sudo ./puppet-enterprise-installer
     ```

   **Tip:** To customize configuration by specifying a different `pe.conf` file other than the existing file, use the -c flag:

     ```
     sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>
     ```

     Your previous `pe.conf` is backed up to `/etc/puppetlabs/enterprise/conf.d/pe-<TIMESTAMP>.conf` and a new `pe.conf` is created at `/etc/puppetlabs/enterprise/conf.d/pe.conf`.

4. When the upgrade is complete on the primary master, run Puppet: `puppet agent -t`.

5. On the primary master replica, run the upgrade script:

     ```
     /opt/puppetlabs/puppet/bin/curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<PUPPET MASTER FQDN>:8140/packages/current/upgrade.bash | sudo bash
     ```

6. If you have compile masters, upgrade your compile masters.

   SSH into each compile master and run:

   ```
   /opt/puppetlabs/puppet/bin/curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<PUPPET MASTER FQDN>:8140/packages/current/upgrade.bash | sudo bash
   ```

7. If you have ActiveMQ hubs and spokes, upgrade your ActiveMQ hubs and spokes.

   SSH into each hub and spoke and run:

   ```
   /opt/puppetlabs/puppet/bin/curl --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem https://<PUPPET MASTER FQDN>:8140/packages/current/upgrade.bash | sudo bash
   ```

8. Upgrade these additional PE infrastructure components.

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



