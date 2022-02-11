# Installation and upgrade known issues

These are the known issues for installation and upgrade in this release.

## Java 1.8.181 can cause directory services to fail in upgrade

Because of changes to LDAP support in Java 8 Update 181, Puppet Enterprise might be unable to connect to external directory services \(DS\) when you upgrade to PE 2018.1.3 or later. This occurs when the hostname PE uses to communicate with the directory service does not match the CN or one DNS altname of the certificate presented by the directory service.

As a workaround, disable the stricter endpoint verification that is the default in Java.

1.  In the console, click **Classification**.

2.  In the **PE Infrastructure** node group, select **PE Console**.

3.  Click **Configuration** and scroll down to the class **puppet\_enterprise::profile::console**.

4.  Click the **Parameter** name list and select **java\_args**.

5.  Add the variable to disable endpoint identification \(and keep any existing heap settings\):

    `{"Xmx": "256m", "Xms": "256m", "Dcom.sun.jndi.ldap.object.disableEndpointIdentification=" : "true" }`

6.  Click **Add Parameter **and then commit changes.
7.  Run Puppet on the appropriate nodes to apply the change.




## Console output is shown when installing in quiet mode

When running the installer in text mode, adding the `-q` option does not successfully activate quiet mode, and the installation process is logged in the console.

## Web-based installation requires a second Puppet run to fully install PE services

Web-based installation includes a single, initial Puppet run, but a second Puppet run is required in order to populate `services.conf`. Until a second Puppet run completes, the Puppet service status in the console reports that one or more services isn't accepting requests.

## Ubuntu 16.10 agents can't be installed with package management

Installation using package management isn't currently supported. If you need to install an Ubuntu 16.10 agent, log into the node where you want to install the agent and run:

```
wget https://apt.puppetlabs.com/puppetlabs-release-pc1-yakkety.deb
sudo dpkg -i puppetlabs-release-pc1-yakkety.deb
```

## **Installer can fail due to SSL errors with AWS**

In some cases, some master platforms have received SSL errors when attempting to connect to s3.amazonaws.com, and so have been unable retrieve the agent packages needed for installation. Updating the CA cert bundle on the master platform usually resolves the problem. To update the bundle, run the following commands:

```
rm /etc/ssl/certs/ca-bundle.crt
yum reinstall ca-certificates
```

After you update the CA bundle, run the installer again.

## Incorrect credentials for console databases cause split upgrade to fail

During a split upgrade, if you supply incorrect credentials for the database associated with the console, including database names, user names, or passwords, the upgrade process fails with an error message. Verify that you're using the correct database credentials and re-run the upgrader. The credentials can be found on the PuppetDB node at `/etc/puppetlabs/installer/database_info.install`.

## Web-based installer fails to acknowledge failed installs due to low RAM

When installation fails because a system is not provisioned with adequate RAM, the web-based installer stops responding, but the **Start using Puppet Enterprise** button is available, suggesting that installation succeeded. In these cases, the command line shows an out of memory: Kill process error.

Provision the system with adequate RAM and re-run the installation.

## Hard tabs for indentation in Hiera YAML files cause errors after upgrading

Before upgrade, ensure that any Hiera YAML files do not use hard tabs for indentation.

## Incorrect umask value can cause installation and upgrade to fail

Set an umask value of `0022` on your master.

## Upgrade fails if `autosign.conf` contains invalid entries

Entries in `/etc/puppetlabs/puppet/autosign.conf` that don't conform to autosign requirements cause the upgrade to fail when configuring the console. Correct any invalid entries before upgrading. For more information about autosigning, see [Config files: autosign.conf](https://puppet.com/docs/puppet/5.5/config_file_autosign.html).

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


## Puppet stdlib functions handle `undef` too leniently

PE 2018.1 uses Puppet 5 stdlib functions. These functions handle `undef` less strictly then they should. In Puppet 6, many functions from stdlib were moved into Forge modules, and now treat `undef` more strictly. Consequently, in PE 2019, some code that relies on `undef`values being implicitly treated as other types will return an evaluation error. For more information on which functions were moved out of stdlib, see the [Puppet 6.0 release notes](https://puppet.com/docs/puppet/6.0/release_notes_puppet.html#select-types-moved-to-modules).

