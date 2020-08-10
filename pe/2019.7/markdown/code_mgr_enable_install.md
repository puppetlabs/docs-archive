# Enable Code Manager during installation

To configure Code Manager during a fresh installation of PE, add parameters to the `pe.conf` file.

Use these parameters **only** with text-mode PE installation, not with web-based installation. Adding the listed parameters enables and configures file sync and Code Manager.

1.  Add the following three parameters to `pe.conf` *before* installation, adding your specific URL and directory information:

    ```no-highlight
    "puppet_enterprise::profile::master::r10k_remote": "git@<YOUR.GIT.SERVER.COM>:puppet/control.git"
    
    "puppet_enterprise::profile::master::r10k_private_key": "/etc/puppetlabs/puppetserver/ssh/id-control_repo.rsa"
    
    puppet_enterprise::profile::master::code_manager_auto_configure": true
                      
    ```

    These parameters specify the private key location and the control repo URL, and enables Code Manager and file sync.

    -   `puppet_enterprise::profile::master::r10k_private_key`

        This setting accepts a string that specifies the path to the future location of the SSH private key used to access your Git repositories. **After** PE installation is completed, place the key in the specified location. You do this in step 3 below.

    -   `"puppet_enterprise::profile::master::r10k_remote"`

        This setting specifies the location of the control repository. It accepts a string that is a valid URL for your Git control repository.

    -   `puppet_enterprise::profile::master::code_manager_auto_configure`

        This setting configures Code Manager, the Git control repository to use for storing code, and the private key for accessing your Git repos. Accepts the values `true` or `false` . Set it to `true` to enable and configure Code Manager. A `false` setting disables Code Manager and file sync.

2.  Complete the installation, following the steps in the text-based installation instructions.

3.  After installation is complete, place the SSH private key you created when you set up your control repository in the `r10k_private_key` location.


Next, set up authentication.

**Related information**  


[Configuration parameters and the pe.conf file](installing_pe.md#)

