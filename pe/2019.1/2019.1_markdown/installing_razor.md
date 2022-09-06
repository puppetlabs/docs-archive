---
author: melissa amos <melissa.amos@puppet.com\>
---

# Installing Razor

After you set up a Razor environment, you're ready to install Razor.

## Install Razor

At a minimum, to install Razor, you must install the Razor server. You can also install the client to make interacting with the server easier, and enable authentication security to control permissions.

### About this task

**Tip:** Use variables to avoid repeatedly replacing placeholder text. For installation, we recommend declaring a server name and the port to use for Razor with these commands:

```
export RAZOR_HOSTNAME=<server name>
export HTTP_PORT=8150
export HTTPS_PORT=8151
```

The installation tasks on this page use `$<RAZOR_HOSTNAME>`, `$<HTTP_PORT>`, and `$<HTTPS_PORT>` to represent these variables.

### Install the Razor server

Installing Razor involves classifying a node with the `pe_razor` module. 

#### About this task

When Puppet Enterprise applies this classification, the software downloads automatically and installs a Razor server and a PostgreSQL database. The download can take several minutes.

Because the Razor software is stored online, you need an internet connection to install it.

**Important:** Don't install the Razor server on your master.

#### Procedure

1.  In the console, create a classification node group for the Razor server.

2.  Click the Razor server node group, click **Configuration**, then in the **Add new class** field, enter `pe_razor` and click **Add class**.

3.  Commit changes.

4.  On the Razor server, run Puppet: `puppet agent -t`


#### Results

**Related information**  


[Create classification node groups](grouping_and_classifying_nodes.md#)

[Running Puppet on nodes](run_puppet_on_nodes.md#)

#### Install the Razor server while you're offline

Install Razor using your own remote directory containing a Puppet Enterprise tarball and the Razor microkernel.

##### Before you begin

Copy the [Razor microkernel](https://pup.pt/razor-microkernel-latest) and the [Puppet Enterprise tarball](https://puppet.com/download-puppet-enterprise) appropriate for your installation to your own FTP site or to the Razor server.

##### About this task

If you save the files locally, when you specify directory paths, use three forward slashes \(`file:///`\). If you save the files on the Razor server, use two forward slashes \(`file://`\).

The example parameter values in this task use this directory structure:

```
[root@razor ~]# tree  /tmp/razor_files/
/tmp/razor_files/
├── 2017.2.3/
│   └── puppet-enterprise-2017.2.3-el-7-x86_64.tar.gz
└── microkernel-008.tar
```

##### Procedure

1.  In the console, create a classification node group for the Razor server.

2.  Click the Razor server node group, click **Configuration**, then in the **Add new class** field, enter `pe_razor` and click **Add class**.

3.  In the **pe\_razor** class, specify the URL for your tarball, and then click **Add parameter**.

    |Parameter|Value|
    |---------|-----|
    |**pe\_tarball\_base\_url** **Note:** This parameter can be used only for installation, not upgrades

|Full path to a directory that contains <PE\_VERSION\>/<PE\_INSTALLER\>.tar.gz, for example `file:///tmp/razor_files`.

 The complete URL is automatically constructed by appending the PE build and file name to the base directory.

|

4.  In the **pe\_razor** class, specify the URL for the microkernel, and then click **Add parameter**.

    |Parameter|Value|
    |---------|-----|
    |**microkernel\_url**|Full path to the microkernel-<VERSION\>.tar, for example `file:///tmp/razor_files/microkernel-008.tar`.

|

5.  Commit changes.

6.  On the Razor server, run Puppet: `puppet agent -t`

7.  \(Optional\) Install the Razor client.

    1.  From a web-enabled machine, fetch these gems:

        ```
        /opt/puppetlabs/puppet/bin/gem fetch colored --version 1.2
        /opt/puppetlabs/puppet/bin/gem fetch command_line_reporter --version 3.3.6
        /opt/puppetlabs/puppet/bin/gem fetch mime-types --version 1.25.1
        /opt/puppetlabs/puppet/bin/gem fetch multi_json --version 1.12.1
        /opt/puppetlabs/puppet/bin/gem fetch razor-client --version 1.9.4
        /opt/puppetlabs/puppet/bin/gem fetch faraday --version 0.15.4
        
        ```

    2.  Copy the downloaded gems to a directory on the Razor server.

    3.  Install the gems on the Razor server: `/opt/puppetlabs/puppet/bin/gem install -f --local *.gem`


##### Results

**Related information**  


[Create classification node groups](grouping_and_classifying_nodes.md#)

[Running Puppet on nodes](run_puppet_on_nodes.md#)

#### Change the default Razor port

The default ports for Razor are port 8150 for HTTP communication between the server and nodes, and port 8151 for HTTPS, used for accessing the public API. You can optionally change the default ports if they’re occupied by another service, out of range, or blocked by a firewall.

##### About this task

##### Procedure

1.  In the console, select the **Classification**, then click the node group that contains the **pe\_razor** module.

2.  On the **Configuration** tab, under **pe\_razor** in the **Parameters** box, select whether you want to change **server\_http\_port** or **server\_https\_port**.

3.  In the **Value** field, enter the port number to use, click **Add parameter**, and then commit changes.


##### Results

#### Parameters for the `pe_razor` class

Parameters for the **pe\_razor** class enable customization of your Razor installation. You can review configuration settings that are currently being used by Razor with the `razor config` command.

The `pe_razor` class has these parameters:

|Parameter|Default|Description|
|---------|-------|-----------|
|`api_config_blacklist`|\[‘facts.blacklist’, ‘database\_url’\]|Properties that Razor hides from query results. You can add additional properties to protect sensitive data.|
|`auth_config`|/etc/puppetlabs/razor-server/shiro.ini|Path to the authentication configuration file.|
|`auth_enabled`|false|`true` to enable authentication for requests to `/api` endpoints, or `false`.|
|`broker_path`|/etc/puppetlabs/razor-server/brokers:brokers|Colon-separated list of directories containing broker types.|
|`checkin_interval`|15|Interval, in seconds, at which the microkernel checks in with the Razor server.|
|`database_url`|jdbc:postgresql:razor?user=razor&sslmode=require&sslcert=/etc/puppetlabs/razor-server/ssl/client.cert.pem&sslkey=/etc/puppetlabs/razor-server/ssl/client.key.pk8|URL for the Razor server.|
|`dbpassword`|razor|Password to the Razor database.|
|`facts_blacklist`|\[‘domain’, ‘filesystems’, ‘fqdn’, ‘hostname’, ‘id’, ‘/kernel.\*/’, ‘memoryfree’, ‘memorysize’, ‘memorytotal’, ‘/operatingsystem.\*/’, ‘osfamily’, ‘path’, ‘ps’, ‘rubysitedir’, ‘rubyversion’, ‘selinux’, ‘sshdsakey’, ‘/sshfp\_\[dr\]sa/’, ‘sshrsakey’, ‘/swap.\*/’, ‘timezone’, ‘/uptime.\*/’\]|Facts that Razor ignores. Each entry can be a string or a regexp enclosed in `/../` where any fact that matches the regexp is dropped.|
|`facts_match_on`|\[\]|Array of values used to match nodes from within the microkernel to nodes in the Razor database. By default, this parameter excludes `/^macaddress.&#42;/` \(regex\),`serialnumber`, and `uuid`, which are already used.|
|`hook_execution_path`|/opt/puppetlabs/puppet/bin|Colon-separated list of paths that Razor searches in order when running hooks, prior to using the default execution path.|
|`hook_path`|/etc/puppetlabs/razor-server/hooks:hooks|Colon-separated list of directories containing hook types.|
|`match_nodes_on`|\[‘mac’\]|Array of values used to match nodes when a node PXE boots. Values can include `mac`, `serial`, `asset`, `oruuid`.|
|`microkernel_debug_level`|quiet|Sets the logging level for the microkernel. Valid values are `quiet` or `debug`.|
|`microkernel_extension_zip`|/etc/puppetlabs/razor-server/mk-extension.zip|Zip file that specifies custom facts or other code that is unpacked by the microkernel prior to checkin.|
|`microkernel_kernel_args`|"|Additional command-line arguments that are supplied to the microkernel during boot.|
|`microkernel_url`|https://pm.puppetlabs.com/puppet-enterprise-razor-microkernel-$|Location of the Razor microkernel used to install Razor offline.|
|`pe_tarball_base_url`|https://pm.puppetlabs.com/puppet-enterprise|Location of the Puppet Enterprise tarball used to install Razor offline.|
|`protect_new_nodes`|false|`true` to make new machines ineligible for provisioning, or `false`.|
|`repo_store_root`|/opt/puppetlabs/server/data/razor-server/repo|Directory where repository contents are downloaded and served.|
|`secure_api`|true|`true` to require HTTPS/SSL communication with `/api` endpoints.|
|`server_http_port`|8150|Port that nodes use to communicate with the server over HTTP. Only URLs starting with `/svc` need to be available on this port.|
|`server_https_port`|8151|Port that the client uses to communicate with the server’s public API over HTTPS. Only URLs starting with `/api` need to be available on this port.|
|`task_path`|/etc/puppetlabs/razor-server/tasks:tasks|Colon-separated list of directories containing tasks.|

### Verify the Razor server

Use a test command to verify that the Razor server is correctly installed. The output JSON file `test.out` contains a list of available Razor commands.

#### About this task

#### Procedure

1.  Test the Razor configuration: 

    ```
    wget https://$<RAZOR_HOSTNAME>:$<HTTPS_PORT>/api -O test.out
    ```


#### Results

### Install the Razor client

The Razor client is installed as a Ruby gem, `razor-client`. The process for installing the client differs by platform.

#### Results

#### Install the Razor client on \*nix systems

Make interacting with the Razor server from \*nix systems easier by installing the Razor client.

##### Procedure

1.  Install the client: `gem install razor-client`

    **Tip:** If you're installing the Razor client on your master, you must specify the location of the gem: `**/opt/puppetlabs/puppet/bin/gem install razor-client**`

2.  Point the Razor client to the server: `razor -u https://$<RAZOR_HOSTNAME>:$<HTTPS_PORT>/api`

    An error displays if the client isn’t installed or can’t connect to the server.

3.  \(Optional\) If you receive a warning message about JSON, you can optionally disable it: `gem install json_pure`


##### Results

#### Install the Razor client on Windows systems

Make interacting with the Razor server from Windows systems easier by installing the Razor client.

##### Procedure

1.  From a Puppet command prompt \( **Start** \> **Start Command Prompt with Puppet** \> **Run as administrator**\), install the client: `gem install razor-client`

2.  Set an environment variable for the Razor server URL: `setx RAZOR_API https://$<RAZOR_HOSTNAME>:8151/api`.

    **Tip:** Alternatively, you can set the variable through **User Accounts** in the **Control Panel**.


##### Results

### Enable authentication security

Enable authentication security to control what tasks users can perform. For example, you might limit certain users to read permissions to avoid accidental overwrite of nodes.

#### About this task

Two methods are required to secure your Razor server:

-   Authentication security using Shiro – disabled by default

-   Protocol security using HTTPS and TLS/SSL – enabled by default if you’re using Puppet Enterprise and you install Razor on a managed node


#### Procedure

1.  In the console, in the **pe\_razor** class, specify Shiro authentication parameters.

    |**Parameter**|**Value**|
    |-------------|---------|
    |**auth\_enabled**|**true**|
    |**auth\_configured**|Location of the Shiro file, `/etc/puppetlabs/razor-server/shiro.ini`|

2.  Specify users, passwords, and roles in your `shiro.ini` file, located at `/etc/puppetlabs/razor-server/shiro.ini`.

    For example, this INI file specifies two users: `razor` is an admin who can perform all functions, and `other` is a user who can perform only read operations, such as viewing collections.

    ```
    [main]
    sha256Matcher = org.apache.shiro.authc.credential.Sha256CredentialsMatcher
    iniRealm.credentialsMatcher = $sha256Matcher 
    
    [users]
    # define users known to shiro, using the format:
    # <username> = <sha256 password hash>, <role>[, <role>...]
    razor = 9b4f1d0e11dcc029c3493d945e44ee077b68978466c0aab6d1ce453aac5f0384, admin
    other = d74ff0ee8da3b9806b18c877dbf29bbde50b5bd8e4dad7a3a725000feb82e8f1, user
    
    [roles]
    # define roles and their associated permissions
    admin = *
    user = query:*
    ```

3.  Restart the Razor service: `service pe-razor-server restart`


#### Results

#### Permissions for Razor

These are the available permissions for Razor.

**Note:** Commands have varying access control patterns. Use `razor <COMMAND_NAME> --help` to view required permissions for each command.

|Task|Permission|
|----|----------|
|Query all collections|`query:*`|
|Query the node collection|`query:nodes`|
|Query a specific node|`query:nodes:<NODE_NAME>`|
|Run all commands|`commands:*`|
|Create policies|`commands:create-policy:*`|
|Create policies starting with a specific name|`commands:create-policy:<NAME>*`|

### Verify Razor versions

Verify your installation by checking the version of the Razor server and client. This information can also be useful for troubleshooting.

#### Procedure

1.  On the client or server, verify versions: `razor --version` or `razor -v`


#### Results

