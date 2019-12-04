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

    3.  On the [EOS download page](http://downloads.puppetlabs.com/eos/4/PC1/i386/), locate the most recent `.swix` package for the agent and copy it: `copy http://downloads.puppetlabs.com/eos/4/PC1/i386/puppet-agent-<VERSION NUMBER>.eos4.i386.swix extension:`

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

You can install agents on network switches running Cisco NX-OS 7.0 and later and IOS-XR 6.0 and later.

Supported Cisco switches include:

-   Cisco Nexus 95xx series switches
-   Cisco Nexus 93xx series switches
-   Cisco Nexus 31xx series switches
-   Cisco Nexus 30xx series switches
-   Cisco IOS-XR NCS5500 \(IOS-XR 6.0\)
-   Cisco IOS-XR XR9kv \(IOS-XR 6.0\)

**Note:** Currently for IOS-XR switches, the agent only supports the built-in package, file, and service resources.

For more information about managing Cisco network switches, see the [puppet-ciscopuppet module](https://forge.puppet.com/puppetlabs/ciscopuppet/readme).

### Install Cisco NX-OS agents

The puppet-ciscopuppet module contains the types and providers needed to configure Cisco NX-OS network switches.

#### Before you begin

If you're installing for bash, you must have networking and DNS configured for the bash environment.

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


### Install Cisco IOS-XR agents

The puppet-ciscopuppet module contains the types and providers needed to configure Cisco IOS-XR network switches.

#### Before you begin

If you're installing for bash, you must have networking and DNS configured for the bash environment.

#### About this task

Cisco IOS-XR agents must be run with bash.

#### Procedure

1.  On your master, install the puppetlabs-ciscopuppet module: `puppet module install puppetlabs-ciscopuppet`

2.  From the network device, start a root Bash shell with networking enabled:

    ```
    run bash
    sudo ip netns exec management bash
    ```

3.  Install the Puppet Collections PC1 repository package:

    ```
    bash-4.2# yum install http://yum.puppetlabs.com/puppetlabs-release-pc1-cisco-wrlinux-7.noarch.rpm
    ```

4.  Install the agent: `yum install puppet-agent`

    The agent is installed in `/opt/puppetlabs`.

5.  Add the`bin` and `lib` paths to your login shell's `PATH`: `export PATH=$PATH:/opt/puppetlabs/puppet/bin:/opt/puppetlabs/puppet/lib`


### Uninstall Cisco agents

The commands to uninstall NX-OS and IOS-XR Cisco agents are the same for both Bash and guest shell environments.

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


## Installing Cumulus Linux agents

PE supports the Cumulus Linux operating system as an agent-only platform.

### Install Cumulus Linux agents

#### Before you begin

You must have access to the Cumulus account on the network device, and you must know:

-   The IP address and the fully qualified domain name \(FQDN\) of your master.
-   The FQDN of the network device that you're installing the Cumulus Linux on.

#### Procedure

1.  From the network switch, retrieve the agent package: `wget http://pm.puppetlabs.com/puppet-agent/2017.3.10/5.3.8/repos/deb/cumulus/PC1/puppet-agent_5.3.8-1cumulus_amd64.deb`

2.  Install the agent package: `sudo dpkg -i puppet-agent_5.3.8-1cumulus_amd64.deb`

3.  Update the package: `sudo apt-get update`

4.  Install the agent: `sudo apt-get install puppet-agent`

5.  Edit `/etc/hosts` `puppet` so that it includes the IP address of your master, as well as its FQDN and default DNS alt name, `puppet`.

6.  Connect the agent to the master and create a certificate signing request for the new agent: `sudo /opt/puppetlabs/bin/puppet agent -t`

7.  On your master, sign the cert for the network device: `puppet cert sign <NETWORK DEVICE FQDN>`

8.  On the network device, run Puppet: `sudo /opt/puppetlabs/bin/puppet agent -t`


#### Results

The agent retrieves its catalog and is fully functional.

### Uninstall Cumulus Linux agents

#### Procedure

1.  Uninstall the agent and agent packages: `sudo apt-get remove puppet-agent puppetlabs-release-pc1`

2.  On your master, revoke the cert for the agent: `puppet cert clean <NETWORK DEVICE FQDN>`


#### Results

The agent certificate is revoked and and related files are deleted from the master.

