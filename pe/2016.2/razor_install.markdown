---
layout: default
title: "Installing Razor"
canonical: "/pe/latest/razor_install.html"

---
At a minimum, to install Razor, you must [install the Razor server](#install-the-razor-server).

We also recommend these tasks:

* [Install the Razor client](#install-the-razor-client) to make interacting with the server easier.
* [Enable authentication security](#enable-authentication-security) to control permissions for Razor.

> **Important**: Don't install the Razor server on your Puppet master.

>**Tip**: Use variables to avoid repeatedly replacing placeholder text. For installation, we recommend declaring a server name and the port to use for Razor with these commands:
>
>     export RAZOR_HOSTNAME=<server name>
>     export HTTP_PORT=8150
>     export HTTPS_PORT=8151
>
> The installation tasks on this page use `$<RAZOR_HOSTNAME>`, `$<HTTP_PORT>` and `$<HTTPS_PORT>` to represent these variables.

## Install the Razor server

Installing Razor involves classifying a node with the `pe_razor` module. When PE applies this classification, the software downloads automatically and installs a Razor server and a PostgreSQL database. The download can take several minutes.

Because the Razor software is stored online, you need an internet connection to install it.

1. In the console, [create a classification node group](./console_classes_groups.html#creating-classification-node-groups) for the Razor server.
2. Click the Razor server node group, click **Classes**, then in the **Class name** field, enter *pe_razor* and click **Add class**..
3. Click **Add class**, then click **Commit**.
4. On the Razor server, run Puppet using `puppet agent -t` or wait for the scheduled agent run.

#### Install Razor while you're offline

Host your own remote directory containing a PE tarball and the Razor microkernel. This directory is used to install Razor. The directory can be hosted on the Razor server, in which case the URL starts with `file://`.

> **Before you begin**: Copy the [Razor microkernel](https://s3-us-west-2.amazonaws.com/razor-releases/microkernel-007.tar) and the [PE tarball](https://puppet.com/download-puppet-enterprise) appropriate for your installation to your own FTP site or to the Razor server.

1. In the console, [create a classification node group](./console_classes_groups.html#creating-classification-node-groups) for the Razor server.
2. Click the Razor server node group, click **Classes**, then in the **Class name** field, enter *pe_razor* and click **Add class**.
3. In the **pe_razor** class, specify the URL for your tarball, then click **Add parameter**.

   Option | Definition
   -------|------------
   Parameter name | pe_tarball_base_url
   Value | URL for the tarball

   The tarball must retain the same name format as on the Puppet server. The default URL is `$<MIRROR>/<PE_BUILD>/puppet-enterprise-<PE_BUILD>-<RELARCH>.tar.gz`, in which `$<MIRROR>` represents the address of the base directory.

   For example, `https://internal.mirror.net/2015.3.3/puppet-enterprise-2015.3.3-el-7-x86_64.tar.gz`.
   
    > **Note:** The pe_tarball_base_url parameter can only be used for installation, not upgrades.  

4. In the **pe_razor** class, specify the URL for the microkernel, then click **Add parameter**.

   Option | Definition
   -------|------------
   Parameter name | microkernel_url
   Value | URL for the microkernal.

   The URL is either your FTP site or, if you copied the microkernel to the Razor server, `file:///path/to/microkernel.tar`.

5. Commit your changes.

6. On the Razor server (the node that you classified with the *pe_razor* class), run Puppet: `puppet agent -t`.

7. (Optional) Install the Razor client.

   1. From a web-enabled machine, fetch these gems:

          /opt/puppetlabs/puppet/bin/gem fetch colored --version 1.2
          /opt/puppetlabs/puppet/bin/gem fetch command_line_reporter --version 3.3.6
          /opt/puppetlabs/puppet/bin/gem fetch mime-types --version 1.25.1
          /opt/puppetlabs/puppet/bin/gem fetch multi_json --version 1.11.2
          /opt/puppetlabs/puppet/bin/gem fetch pe-razor-client --version 1.1.0
          /opt/puppetlabs/puppet/bin/gem fetch rest-client --version 1.6.9

   2. Copy the downloaded gems to a directory on the Razor server.
   3. Install the gems on the Razor server: `/opt/puppetlabs/puppet/bin/gem install -f --local *.gem`.

#### Change the default Razor port

The default ports for Razor are port 8150 for HTTP communication between the server and nodes, and port 8151 for HTTPS, used for accessing the public API. You can optionally change the default ports if they're occupied by another service, out of range, or blocked by a firewall.

1. Select the **Classification** tab, then click the node group that contains the `pe_razor` module.
2. Select the **Classes** tab, then under **pe_razor** in the **Parameters** box, select whether you want to change `server_http_port` or `server_https_port`.
3. In the **Value** field, enter the port number to use, click **Add parameter**, then click **Commit**.

#### Parameters for the `pe_razor` module

Class parameters for the `pe_razor` module enable customization of your Razor installation. You can review configuration settings that are currently being used by Razor with the `razor config` command.

The `pe_razor` module has these parameters:

| Parameter                   | Default                           | Description |
|-----------------------------|-----------------------------------|-------------|
| api_config_blacklist        | [ 'facts.blacklist', 'database_url'] | Properties that Razor hides from query results. You can add additional properties to protect sensitive data.
| auth_config                 | /etc/puppetlabs/razor-server/shiro.ini | Path to the authentication configuration file.
| auth_enabled                | false                             | `true` to enable authentication for requests to `/api` endpoints.
| broker_path                 | /etc/puppetlabs/razor-server/brokers:brokers | Colon-separated list of directories containing broker types.
| checkin_interval            | 15                                | Interval, in seconds, at which the microkernel checks in with the Razor server.
| database_url                | jdbc:postgresql:razor?user=razor&password=$dbpassword| URL for the Razor server.
| dbpassword                  | razor                             | Password to the Razor database.
| facts_blacklist             | ['domain', 'filesystems', 'fqdn', 'hostname', 'id', '/kernel.&#42;/', 'memoryfree', 'memorysize', 'memorytotal', '/operatingsystem.&#42;/', 'osfamily', 'path', 'ps', 'rubysitedir', 'rubyversion', 'selinux', 'sshdsakey', '/sshfp_[dr]sa/', 'sshrsakey', '/swap.&#42;/', 'timezone', '/uptime.&#42;/'] | Facts that Razor ignores. Each entry may be a string or a regexp enclosed in `/../` where any fact that matches the regexp is dropped.
| facts_match_on              | []                                | Array of values used to match nodes from within the microkernel to nodes in Razor's database. By default, this parameter excludes `/^macaddress.&#42;/` (regex), `serialnumber`, and `uuid`, which are already used.
| hook_execution_path         | /opt/puppetlabs/puppet/bin        | Colon-separated list of paths that Razor searches in order when running hooks, prior to using the default execution path.
| hook_path                   | /etc/puppetlabs/razor-server/hooks:hooks | Colon-separated list of directories containing hook types.
| match_nodes_on              | ['mac']                           | Array of values used to match nodes when a node PXE boots. Values can include `mac`, `serial`, `asset`, or `uuid`.
| microkernel_debug_level     | quiet                             | Sets the logging level for the microkernel. Valid values are `quiet` or `debug`.
| microkernel_extension_zip   | /etc/puppetlabs/razor-server/mk-extension.zip | Zip file that specifies custom facts or other code that is unpacked by the microkernel prior to checkin.
| microkernel_kernel_args     | ''                                | Additional command-line arguments that are supplied to the microkernel during boot.
| microkernel_url             | https://pm.puppetlabs.com/puppet-enterprise-razor-microkernel-$<VERSION> | Location of the Razor microkernel used to install Razor offline.
| pe_tarball_base_url         | https://pm.puppetlabs.com/puppet-enterprise | Location of the Puppet Enterprise tarball used to install Razor offline.
| protect_new_nodes           | false                             | **true** to make new machines ineligible for provisioning.
| repo_store_root             | /opt/puppetlabs/server/data/razor-server/repo | Directory where repository contents are downloaded and served.
| secure_api                  | true                              | **true** to require HTTPS/SSL communication with `/api` endpoints.
| server_http_port            | 8150                              | Port that nodes use to communicate with the server over HTTP. Only URLs starting with `/svc` need to be available on this port.
| server_https_port           | 8151                              | Port that the client uses to communicate with the server's public API over HTTPS. Only URLs starting with `/api` need to be available on this port.
| task_path                   | /etc/puppetlabs/razor-server/tasks:tasks | Colon-separated list of directories containing tasks.

## Verify the Razor server
Use a test command to verify that the Razor server is correctly installed. The output JSON file `test.out` contains a list of available Razor commands.

1. Test the Razor configuration: `wget https://$<RAZOR_HOSTNAME>:$<HTTPS_PORT>/api -O test.out`.


## Install the Razor client

The Razor client is installed as a Ruby gem, `pe-razor-client`. The process for installing the client differs by platform.

**Note**: Don't install the PE client, `pe-razor-client`, alongside the open source Razor client, `razor-client`. If you already have `razor-client` installed, or are not sure if you do, run `gem uninstall razor-client` before installing the PE client.

#### Install the Razor client on *nix systems

1. Install the client: `gem install pe-razor-client`.
2. Point the Razor client to the server: `razor -u https://$<RAZOR_HOSTNAME>:$<HTTPS_PORT>/api`.

   An error displays if the client isn't installed or can't connect to the server.

3. (Optional) If you receive a warning message about JSON, you can optionally disable it: `gem install json_pure`.

#### Install the Razor client on Windows systems

1. From a Puppet command prompt (**Start** > **Start Command Prompt with Puppet** > **Run as administrator**), install the client: `gem install pe-razor-client`.
2. Set an environment variable for the Razor server URL: `setx RAZOR_API https://$<RAZOR_HOSTNAME>:8151/api`.

   **Tip**: Alternatively, you can set the variable through **User Accounts** in the Control Panel.


## Enable authentication security

Two methods are required to secure your Razor server:

* Authentication security using Shiro -- disabled by default
* Protocol security using HTTPS and TLS/SSL -- enabled by default if you're using PE and install Razor on a Puppet-managed node

Enable authentication security to control what tasks users can perform. For example, you might limit certain users to read permissions to avoid accidental overwrite of nodes.

1. Specify Shiro authentication. In the `pe_razor` module, modify these settings:

   | Parameter | Value |
   |-----------|-------|
   | `auth_enabled` | `true` |
   | `auth_configured` | Location of the Shiro file, `/etc/puppetlabs/razor-server/shiro.ini` |

2. Specify users and roles in your `shiro.ini` file, located at `/etc/puppetlabs/razor-server/shiro.ini`.

   For example, this INI file specifies two users: *razor* is an admin who can perform all functions, and *other* is a user who can perform only read operations, such as viewing collections.


   ~~~
    [main]

    [users]
    # define users known to shiro, using the format:
    # <username> = <password>, <role>[, <role>...]
    razor = razor, admin
    other = pass, user

    [roles]
    # define roles and their associated permissions
    admin = *
    user = query:*
   ~~~

   For more details, see Apache's guide to [INI configuration](http://shiro.apache.org/configuration.html#Configuration-INISections).

3. Restart the Razor service: `service pe-razor-server restart`.

### Permissions for Razor

These are the available permissions for Razor.

**Note**: Commands have varying access control patterns. Use `razor <COMMAND_NAME> --help` to view required permissions for each command.

|Task           |Permission |
|---------------|-----------|
|Query all collections | `query:*` |
|Query the node collection |`query:nodes`|
|Query a specific node | `query:nodes:<NODE_NAME>`|
|Run all commands | `commands:*`|
|Create policies |`commands:create-policy:*`|
|Create policies starting with a specific name |`commands:create-policy:<NAME>*`|



## Verify Razor versions

Verify your installation by checking the version of the Razor server and client. This information can also be useful for troubleshooting.

1. On the client or server, enter `razor --version` or `razor -v`.

* * *
