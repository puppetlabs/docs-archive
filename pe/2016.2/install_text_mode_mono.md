---
layout: default
title: "Text-mode installation: monolithic"
canonical: "/pe/latest/install_text_mode_mono.html"
---

The following instructions are for performing a text-mode monolithic installation of PE. This installs the master, console, and PuppetDB components all on the same machine.

See [Choosing a PE architecture](./install_system_requirements.html#choosing-a-pe-architecture) for more information about installation types.

Before beginning, review [the text mode installation overview](./install_text_mode.html).

## Install PE

1. [Download and verify the appropriate PE tarball](./install_basic.html#downloading-puppet-enterprise).
2. Unpack the tarball by running `tar -xf <TARBALL_FILENAME>`. This requires about 1 GB of space in `/tmp`.
3. From the installer directory, run the installer. The installation steps will vary depending on the path you choose.

   * To use a `pe.conf` file that already exists, run the installer **with the `-c` flag** pointed at the `pe.conf` file:

     ~~~
     sudo ./puppet-enterprise-installer -c <FULL PATH TO pe.conf>
     ~~~

   * To have the installer open a copy of `pe.conf` for you to edit and then use, run the installer **without the `-c` flag**:

     ~~~
     sudo ./puppet-enterprise-installer
     ~~~
     
     >**Warning:** Do not forget to set the console admin password in `pe.conf`.
     
4. Select "text-mode" when prompted. This opens your text editor with a `pe.conf` file that you can edit to match your infrastructure and add any additional text-mode installation options as needed. In a monolithic installation, the the following parameter is required:
      
      - `"console_admin_password": "<PASSWORD>"`— The password used to log into the PE console      
      
      Additionally, if you have an external PostgreSQL node or you have customized the names of the databases in your installation please refer to the [external PostgreSQL parameters in the pe.conf reference](./install_pe_conf_param.html#external-postgresql-parameters).
     
     When you save and close the file, the installer runs.

   >**Tip**: On a monolithic install, you can use the default value of `"%{::trusted.certname}"`if the certname of your monolithic server matches its FQDN.

5. After the installation completes, run Puppet on the monolithic Puppet master by running `puppet agent -t`.

    When the installation completes, you can find an updated `pe.conf` file at `/etc/puppetlabs/enterprise/conf.d`.

### Mandatory parameters for `pe.conf`

Refer to the [`pe.conf` parameter reference](./install_pe_conf_param.html) for a list of mandatory parameters needed to install PE.

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


