---
layout: default
title: "Text-mode installation: monolithic"
canonical: "/pe/latest/install_text_mode_mono.html"
---

The following instructions are for performing a text-mode monolithic installation of PE. This installs the master, console, and PuppetDB components all on the same machine.

See [Choosing a PE architecture](./sys_req_hw.html#choosing-a-pe-architecture) for more information about installation types.

Before beginning, review [the text mode installation overview](./install_text_mode.html).

## Install PE

1. [Download and verify the appropriate PE tarball](./install_basic.html#downloading-puppet-enterprise).
2. Unpack the tarball by running `tar -xf <TARBALL_FILENAME>`. This requires about 1 GB of space in `/tmp`.
3. From the installer directory, run the installer. The installation steps will vary depending on the path you choose.

   * To have the installer open a copy of `pe.conf` for you to edit and install with, run the installer **without the `-c` flag**.

     ~~~
     sudo ./puppet-enterprise-installer
     ~~~
      
   * To use a `pe.conf` file that you've previously populated, run the installer **with the `-c` flag** pointed at the `pe.conf` file.

     ~~~
     sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>
     ~~~
     
     >**Warning:** If you're using a pre-populated file, be sure you've set the parameters correctly as detailed in the following steps.
  
4. If you ran the installer **without the -c flag**, select "text-mode" when prompted. 

   This opens your text editor with a `pe.conf` file that you can edit to match your infrastructure and add any additional text-mode installation options as needed. 

5. Set the following parameter:
   
   Parameter | Value|
   ---------|--------
   `"console_admin_password":` | `"<PASSWORD>"`— Replace `<PASSWORD>` with your password used to log into the PE console.
   
   >**Tip**: On a monolithic install, you can use the default value of `"%{::trusted.certname}"`if the certname of your monolithic server matches its FQDN.      
   
6. If you have an external PostgreSQL server, refer to the [external PostgreSQL parameters in the pe.conf reference](./install_pe_conf_param.html#external-postgresql-parameters) and add them to `pe.conf`.
   
7. Save and close the file.

   This starts the installation.

8. After the installation completes, run Puppet on the monolithic Puppet master by running `puppet agent -t`.

    When the installation completes, you can find an updated `pe.conf` file at `/etc/puppetlabs/enterprise/conf.d`.

### Text-mode installation options

As described above, the syntax for the text-based installation with a specified `pe.conf` file is:

~~~
sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>
~~~
    
When you use the `-c` option, the `pe.conf` file name with full path is required. The command will quit with an error message if you omit it.

When you use the `-c` option, you can also use the following options:

- `-D`: The installer will display debugging information.
- `-q`: The installer will run in quiet mode. The installation process will not be displayed. If errors occur during the installation, the command will quit with an error message.
- `-V`: The installer will display very verbose debugging information.
- `-h`: The installer will display help information.

To see the help information for the command, run:

~~~
sudo ./puppet-enterprise-installer -h
~~~

## Rerunning the installer

The PE installer is *idempotent*, meaning that you can run it as many times as needed without affecting the outcome. If you encounter errors during installation, you can fix them and run the installer again.

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

- [Installing the PE license key](./install_license_key.html)

