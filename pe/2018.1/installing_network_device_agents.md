---
author: melissa amos <melissa.amos@puppet.com\>
---

# Installing network device agents

Install agents on network switches to operate them with Puppet Enterprise as managed devices.

## Installing Arista EOS agents

You can run agents on Arista EOS network switches.

### Install Arista EOS agents

#### About this task

#### Procedure

1.  On your master, install the netdev\_stdlib\_eos module: `puppet module install aristanetworks-netdev_stdlib_eos`

    This module contains the types and providers needed to run the Puppet agent on the network switch.

2.  Install the agent on the network device.

    1.  Access your network device as an admin user, or as a user with access to Privileged EXEC mode.

    2.  Enable Privileged EXEC mode: `enable`

    3.  On the [EOS download page](http://http://downloads.puppetlabs.com/eos/puppet5/4/i386/), locate the most recent `.swix` package for the agent and copy it: `copy http://downloads.puppetlabs.com/eos/4/PC1/i386/puppet-agent-<VERSION NUMBER>.eos4.i386.swix extension:`

        **Note:** If you’re unable to access the Internet from your EOS instance, download the agent package and transfer it to your instance.

    4.  Install the agent on the network device: `extension puppet-agent-<VERSION NUMBER>-eos-4-i386.swix`

    5.  Log out and log back into the network device as `root`.

    6.  Set Puppet to run as the `root` user and group:

        ```
        /opt/puppetlabs/bin/puppet config --confdir /persist/sys/etc/puppetlabs/puppet set user root
        /opt/puppetlabs/bin/puppet config --confdir /persist/sys/etc/puppetlabs/puppet set group root
        ```

    7.  Configure the agent to connect to your master: `puppet config set server <PUPPET MASTER FQDN>`

    8.  Connect the agent to the master and create a certificate signing request for the new agent: `puppet agent --test`

3.  On your master, sign the cert for the network device: `puppet cert sign <NETWORK DEVICE FQDN>`

4.  On the network device, run Puppet: `puppet agent -t`


#### Results

The agent retrieves its catalog and is fully functional.

You see a message like:

```
   Info: Retrieving pluginfacts
   Info: Retrieving plugin
   Info: Loading facts
   Info: Caching catalog for <EOS INSTANCE FQDN>
   Info: Applying configuration version '1424214157'
   Notice: Finished catalog run in 0.46 seconds
```

### Uninstall Arista EOS agents

#### About this task

**Note:** If you're uninstalling and reinstalling the agent for testing purposes, you must follow these instructions completely to ensure you don't get SSL collisions when reinstalling.

#### Procedure

1.  Access your network device instance as an admin user.

2.  Enable Privileged EXEC mode: `enable`

3.  Delete the network device agent extension:

    ```
    no extension puppet-enterprise-<VERSION NUMBER>-eos-4-i386.swix
    delete extension:puppet-enterprise-<VERSION NUMBER>-eos-4-i386.swix
    ```

4.  Delete the SSL keys from the EOS instance: `bash sudo rm -rf /persist/sys/etc/puppetlabs/`

5.  On your master, revoke the cert for the agent on the network device instance: `puppet cert clean <EOS INSTANCE FQDN>`


#### Results

The agent certificate is revoked and and related files are deleted from the master.

You see a message like:

```
   Notice: Revoked certificate with serial 10
   Notice: Removing file Puppet::SSL::Certificate <EOS INSTANCE FQDN> at '/etc/puppetlabs/puppet/ssl/ca/signed/<EOS INSTANCE FQDN>.pem'
   Notice: Removing file Puppet::SSL::Certificate <EOS INSTANCE FQDN> at '/etc/puppetlabs/puppet/ssl/certs/<EOS INSTANCE FQDN>.pem'
```

## Installing Cisco agents

You can install agents on network switches running Cisco NX-OS 7.0 and later.

The [ciscopuppet module](https://forge.puppet.com/puppetlabs/ciscopuppet/readme) allows you to manage Cisco Nexus Network Elements using Puppet. For a full list of supported platforms and limitations, see the [Resource Platform Support Matrix](https://github.com/cisco/cisco-network-puppet-module#resource-platform-support-matrix).

The [Cisco IOS module](https://forge.puppet.com/puppetlabs/cisco_ios) allows you to configure Cisco Catalyst devices running IOS.

### Install Cisco NX-OS agents

The puppet-ciscopuppet module contains the types and providers needed to configure Cisco NX-OS network switches.

#### Before you begin

If you're installing for bash, you must have networking and DNS configured for the bash environment.

**Important:** You cannot install the bash environment agent on NX-OS 9.2\(1\) or newer.

#### About this task

NX-OS supports two possible environments for running third-party software. You can run Puppet from either environment but not both at the same time.

-   Bash — Native Linux environment underlying NX-OS. It is disabled by default.

-   Guest — Secure Linux container environment running CentOS. It is enabled by default on most Nexus platforms


#### Procedure

1.  On your master, install the puppetlabs-ciscopuppet module: `puppet module install puppetlabs-ciscopuppet`

2.  From the network device, start a root bash or guest shell. For a bash install, enable networking:

    ```
    
                         `run bash
    sudo ip netns exec management bash`
                      
    ```

3.  Install the Puppet Collections PC1 repository package:

    -   Bash

        ```
        bash-4.2# yum install http://yum.puppetlabs.com/puppetlabs-release-pc1-cisco-wrlinux-5.noarch.rpm
        ```

    -   Guest

        ```
        sudo su -
        yum install http://yum.puppetlabs.com/puppetlabs-release-pc1-el-7.noarch.rpm
        ```

4.  Install the agent: `yum install puppet-agent`

    The agent is installed in `/opt/puppetlabs`.

5.  Add the`bin` and `lib` paths to your login shell's `PATH`: `export PATH=$PATH:/opt/puppetlabs/puppet/bin:/opt/puppetlabs/puppet/lib`


### Uninstall Cisco agents

The commands to uninstall NX-OS Cisco agents are the same for both Bash and guest shell environments.

#### About this task

#### Procedure

1.  Remove the agent:

    ```
    sudo su -
    yum remove puppet-agent
    ```

2.  Remove the Puppet Collections PC1 repository package package:

    ```
    `yum remove puppetlabs-release-pc1`
    ```


