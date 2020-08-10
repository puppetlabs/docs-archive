# Configuring r10k

When performing a fresh text-mode installation of Puppet Enterprise \(PE\), you can configure r10k by adding parameters to the `pe.conf` file. In existing installations, configure r10k by adjusting parameters in the console.

CAUTION:

Do not edit the r10k configuration file manually. Puppet manages this configuration file automatically and undoes any manual changes you make.

**Related information**  


[Configuration parameters and the pe.conf file](installing_pe.md#)

## Upgrading from previous versions of r10k

If you used r10k prior to PE version 2015.3, you might have configured it in the console using the `pe_r10k` class. We suggest configuring r10k in the master profile class, and then customizing your configuration as needed in Hiera.

This simplifies configuration, and makes it easier to move to Code Manager in the future.

To switch to master profile class configuration, remove the `pe_r10k` class in the console, and then configure r10k as described in the topic about configuring r10k after PE installation. You can then customize your configuration in Hiera if needed.

**Note:** If you were using earlier versions of r10k with the `zack-r10k` module, discontinue use of the module and switch to the master profile configuration as above.

## Configure r10k during installation

To set up r10k during PE installation, add the r10k parameters to `pe.conf` ***before*** starting installation. This is the easiest way to set up r10k with a new PE installation.

Ensure that you have a Puppetfile and a control repo. You also need the SSH private key that you created when you made your control repo.

**Restriction:** This configuration method works only with text-based installation. If you are using the web-based installer, see the related topic about configuring r10k after PE installation.

1.  Add `puppet_enterprise::profile::master::r10k_remote` to `pe.conf`.

    This setting specifies the location of the control repository. It accepts a string that is a valid URL for your Git control repository.

    ```
    "puppet_enterprise::profile::master::r10k_remote": "git@<YOUR.GIT.SERVER.COM>:puppet/control.git"
    ```

2.  Add `puppet_enterprise::profile::master::r10k_private_key` to `pe.conf`.

    This setting specifies the path to the file that contains the SSH private key used to access your Git repositories. This location for the SSH private key file, which you created when you set up your control repository, **must** be located on the Puppet master and owned by and accessible to the `pe-puppet` user. The setting accepts a string.r10k

    ```
    "puppet_enterprise::profile::master::r10k_private_key": "/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa"
    
    ```

3.  Complete PE installation. The installer configures r10k for you. You can change the values for the remote and the private key as needed in the master profile settings in the console.

4.  After PE installation is complete, place the SSH private key you created when you set up your control repository in the `r10k_private_key` location.

5.  Run r10k. PE does not automatically run r10k.


## Configure r10k after PE installation

To configure r10k in an existing PE, set r10k parameters in the console. You can also adjust r10k settings in the console.

Ensure that you have a Puppetfile and a control repo. You also need the SSH private key that you created when you made your control repo.

1.  In the console, set the following parameters in the `puppet_enterprise::profile::master` class in the **PE Master** node group:

    -   `r10k_remote`

        This is the location of your control repository. Enter a string that is a valid URL for your Git control repository, such as `"git@<YOUR.GIT.SERVER.COM>:puppet/control.git"`.

    -   `r10k_private_key`

        This is the path to the private key that permits the `pe-puppet` user to access your Git repositories. This file must be located on the Puppet master, owned by the `pe-puppet` user, and in a directory that the `pe-puppet` user has permission to view. We recommend `/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa`. Enter a string, such as `"/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa"`.

2.  Run Puppet on all of your masters.


You can now customize your r10k configuration in Hiera, if needed. After r10k is configured, you can deploy your environments from the command line. PE does not automatically run r10k at the end of installation.

