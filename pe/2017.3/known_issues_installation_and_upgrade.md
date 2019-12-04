# Installation and upgrade known issues

These are the known issues for installation and upgrade in this release.

## Ubuntu 16.10 agents can't be installed with package management

Installation using package management isn't currently supported. If you need to install an Ubuntu 16.10 agent, log into the node where you want to install the agent and run:

```
wget https://apt.puppetlabs.com/puppetlabs-release-pc1-yakkety.deb
sudo dpkg -i puppetlabs-release-pc1-yakkety.deb
```

## Incorrect credentials for console databases cause split upgrade to fail

During a split upgrade, if you supply incorrect credentials for the database associated with the console, including database names, user names, or passwords, the upgrade process fails with an error message. Verify that you're using the correct database credentials and re-run the upgrader. The credentials can be found on the PuppetDB node at `/etc/puppetlabs/installer/database_info.install`.

## Web-based installer fails to acknowledge failed installs due to low RAM

When installation fails because a system is not provisioned with adequate RAM, the web-based installer stops responding, but the **Start using Puppet Enterprise** button is available, suggesting that installation succeeded. In these cases, the command line shows an of memory: Kill process" error.

Provision the system with adequate RAM and re-run the installation.

## Hard tabs for indentation in Hiera YAML files cause errors after upgrading

Before upgrade, ensure that any Hiera YAML files do not use hard tabs for indentation.

## Incorrect umask value can cause installation and upgrade to fail

Set an umask value of `0022` on your master.

## Upgrade fails if `autosign.conf` contains invalid entries

Entries in `/etc/puppetlabs/puppet/autosign.conf` that don't conform to autosign requirements cause the upgrade to fail when configuring the console. Correct any invalid entries before upgrading.

## Install agents with different OS when master is behind a proxy

If your master uses a proxy server to access the internet, you might not be able to download `pe_repo` packages for the agent.

If you're using a proxy, follow this workaround:

1.  From your master, navigate to `/etc/sysconfig/`, and create a file called `puppet` with this code: 

    ```
    export http_proxy <YOUR_PROXY_SERVER>
       export https_proxy <YOUR_PROXY_SERVER>
    ```

2.  Save and close the file, and then restart the `puppet` service:

```
puppet resource service puppet ensure=stopped
   puppet resource service puppet ensure=running
```

3.  Repeat these steps on any compile masters in your environment.


