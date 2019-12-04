---
layout: default
title: "Text-mode installation: split"
canonical: "/pe/latest/install_text_mode_split.html"
---

The split installation is the base installation type for a large environment, which you'd use to manage more than 20,000 nodes. If you're not managing this many nodes, we recommend the monolithic (or monolithic with compile masters) installation type.

## Install PE: split installation

When you perform a split PE install in text-mode, you pass the installer and the `pe.conf` file to each machine on which your installing component. 

The PE installer is *idempotent*, meaning that you can run it as many times as needed without affecting the outcome. If you encounter errors during installation, you can fix them and run the installer again.

It's critical that you install the components in the order specified:

1. Install the Puppet master.
2. Install PuppetDB.
3. Install the PE console.

Before you begin:

- Review the [text mode installation overview](./install_text_mode.html)
- [Download and verify the appropriate PE tarball](./install_basic.html#downloading-puppet-enterprise)

### Install the Puppet master

When you set up a split installation of PE, you first install the Puppet master.

1. Unpack the tarball by running `tar -xf <TARBALL_FILENAME>`. This requires about 1 GB of space in `/tmp`.
2. From the installer directory, run the installer. The installation steps will vary depending on the path you choose.

   * To have the installer open a copy of `pe.conf` for you to edit and install with, run the installer **without the `-c` flag**.

     ~~~
     sudo ./puppet-enterprise-installer
     ~~~
      
   * To use a `pe.conf` file that you've previously populated, run the installer **with the `-c` flag** pointed at the `pe.conf` file.

     ~~~
     sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>
     ~~~
     
     >**Warning:** If you're using a pre-populated file, be sure you've set the parameters correctly as detailed in the following steps. 
  
3. If you ran the installer **without the -c flag**, select "text-mode" when prompted. 

   This opens your text editor with a `pe.conf` file that you can edit to match your infrastructure and add any additional text-mode installation options as needed. 

4. Set the following parameters:

   >**Important:** Do not use single quotes on parameter values. Use double quotes as shown in the examples.
   
   >**Note:** In the following examples, `<FQDN>` is the fully qualified domain name of the server. For example, the parameter and value should be expressed as `"puppet_enterprise::puppet_master_host": "master.example.com"`. 

   Parameter | Value|
   ---------|-------
   `"console_admin_password":` | `"<PASSWORD>"`— Replace `<PASSWORD>` with your password used to log into the PE console.      
   `"puppet_enterprise::puppet_master_host":`| `"<FQDN>"`— Provide the`<FQDN>` of the node hosting the Puppet master.
   `"puppet_enterprise::console_host":` | `"<FQDN>"`— Provide the`<FQDN>` of the node hosting the PE console.
   `"puppet_enterprise::puppetdb_host":` | `"<FQDN>"`— Provide the`<FQDN>` of the node hosting PuppetDB.
    
   >**Warning**: In a split install, do not use the "%{::trusted.certname}" value for the Puppet master. 
      
5. If you have an external PostgreSQL server, refer to the [external PostgreSQL parameters in the pe.conf reference](./install_pe_conf_param.html#external-postgresql-parameters) and add them to `pe.conf`.
 
6. Save and close the file.

   This starts the installation.
   
7. When the installation completes, transfer the PE installer and the `pe.conf` file (generated at `/etc/puppetlabs/enterprise/conf.d/`) to the server that will become your PuppetDB node.


### Install PuppetDB

In a split installation, after you install the Puppet master, you're ready to install PuppetDB.

Before beginning:

- You should have [installed the Puppet master](#install-the-puppet-master).

1. Unpack the installation tarball by running `tar -xf <TARBALL_FILENAME>`. This requires about 1 GB of space in `/tmp`.
2. If the `pe.conf` parameter `puppet_enterprise::puppet_master_host` is set to `"%{::trusted.certname}"`, change the value to the FQDN of the Puppet master.
3. From the installer directory, run the installer with `sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>`.
4. When the installation completes, transfer the PE installer and the `pe.conf` file to the server that will become your PE console node.

### Install the PE console

After you install the Puppet master and PuppetDB in your split installation, you're ready to install the PE console.

Before beginning:

- You should have [installed the Puppet master](#install-the-puppet-master) and [PuppetDB](#install-puppetdb).

1. Unpack the tarball by running `tar -xf <TARBALL_FILENAME>`. This requires about 1 GB of space in `/tmp`.
2. If the `pe.conf` parameter `puppet_enterprise::puppet_master_host` is set to `"%{::trusted.certname}"`, change the value to the FQDN of the Puppet master.
3. From the installer directory, run the installer with `sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>`.
4. When the installation completes, continue to run Puppet on all split nodes.

### Run Puppet on all split nodes

To complete the split installation, run Puppet on all nodes in the split installation in the order given.

1. Run Puppet on the Puppet master node.
2. Run Puppet on the PuppetDB node.
3. Run Puppet on the Puppet master node a second time.
4. Run Puppet on the PE console node.


## Text-mode installation options

When you run the installer in text mode, you can pass additional flags to control the installation.

The syntax for the text-based installation with a specified `pe.conf` file is

~~~
sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>
~~~

When you use the `-c` option, the `pe.conf` file name with full path is required. The command will quit with an error message if you omit it.

When you use the `-c` option, you can also use the following options:

Option          | Definition  |
----------------|---------------|
`-D` | The installer will display debugging information | 
`-q` | The installer will run in quiet mode. The installation process will not be displayed. If errors occur during the installation, the command will quit with an error message. | 
`-V` | The installer will display very verbose debugging information. |
`-h` | The installer will display help information.

## Add Puppet Enterprise binaries to `PATH`

PE [installs a number of binaries](./install_what_and_where.html#executable-binaries-and-symlinks) for interacting with its tools and services. You should include these binaries in your default path.

1. To include these binaries in your default `$PATH`, manually add them to your profile or run:
   
   ~~~
   PATH=/opt/puppetlabs/puppet/bin:/opt/puppetlabs/server/bin:$PATH;export PATH
   ~~~
      
## Disable Puppet Enterprise symlinks

PE [installs a number symlinks](./install_what_and_where.html#where-is-it-installed) for interacting with its tools and services. If needed, you can disable symlinks by placing a setting in your your [default Hiera file](./config_intro.html#configure-settings-with-hiera).

1. In your default Hiera file, add the following setting:

   ~~~
   puppet_enterprise::manage_symlinks: false
   ~~~

************
**Related links**

- [Install the PE license key](./install_license_key.html)

