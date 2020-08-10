# Uninstalling

Puppet Enterprise includes a script for uninstalling. You can uninstall component infrastructure nodes or from agent nodes.

## Uninstall infrastructure nodes

The `puppet-enterprise-uninstaller` script is installed on the master. In order to uninstall, you must run the uninstaller on each infrastructure node.

By default, the uninstaller removes the software, users, logs, cron jobs, and caches, but it leaves your modules, manifests, certificates, databases, and configuration files in place, as well as the home directories of any users it removes.

1.  From the infrastructure node that you want to uninstall, from the command line as root, navigate to the installer directory and run the uninstall command: `$ sudo ./puppet-enterprise-uninstaller`

    **Note:** If you don't have access to the installer directory, you can run `/opt/puppetlabs/bin/puppet-enterprise-uninstaller`.

2.  Follow prompts to uninstall.

3.  \(Optional\) If you don't uninstall the master, and you plan to reinstall on an infrastructure node at a later date, remove the agent certificate for that component from the master. On the master: `puppetserver ca clean <PE COMPONENT CERT NAME>`.


## Uninstall agents

You can remove the agent from nodes that you no longer want to manage.

**Note:** Uninstalling the agent doesn't remove the node from your environment. To completely remove all traces of a node, you must also purge the node.

**Related information**  


[Adding and removing agent nodes](adding_and_removing_nodes.md#)

### Uninstall \*nix agents

The \*nix agent package includes an uninstall script, which you can use when you're ready to retire a node.

1.  On the agent node, run the uninstall script: `run /opt/puppetlabs/bin/puppet-enterprise-uninstaller`

2.  Follow prompts to uninstall.

3.  \(Optional\) If you plan to reinstall on the node at a later date, remove the agent certificate for the agent from the master: `puppetserver ca clean <AGENT CERT NAME>`


### Uninstall Windows agents

To uninstall the agent from a Windows node, use the Windows **Add or Remove Programs** interface, or uninstall from the command line.

Uninstalling the agent removes the Puppet program directory, the agent service, and all related registry keys. The data directory remains intact, including all SSL keys. To completely remove Puppet from the system, manually delete the data directory.

1.  Use the Windows **Add or Remove Programs** interface to remove the agent.

    Alternatively, you can uninstall from the command line if you have the original .msi file or know the product code of the installed MSI, for example: `msiexec /qn /norestart /x [puppet.msi|<PRODUCT_CODE>]`

2.  \(Optional\) If you plan to reinstall on the node at a later date, remove the agent certificate for the agent from the master: `puppetserver ca clean <AGENT CERT NAME>`


### Uninstall macOS agents

Use the command line to remove all aspects of the agent from macOS nodes.

1.  On the agent node, run these commands:

    ```
    rm -rf /var/log/puppetlabs
    rm -rf /var/run/puppetlabs
    pkgutil --forget com.puppetlabs.puppet-agent
    launchctl remove puppet
    rm -rf /Library/LaunchDaemons/com.puppetlabs.puppet.plist  
    launchctl remove pxp-agent  
    rm -rf /Library/LaunchDaemons/com.puppetlabs.pxp-agent.plist
    rm -rf /etc/puppetlabs
    rm -rf /opt/puppetlabs
    ```

2.  \(Optional\) If you plan to reinstall on the node at a later date, remove the agent certificate for the agent from the master: `puppetserver ca clean <AGENT CERT NAME>`


## Uninstaller options

You can use the following command-line flags to change the uninstaller's behavior.

-   `-p` — Purge additional files. With this flag, the uninstaller also removes all configuration files, modules, manifests, certificates, the home directories of any users created by the installer, and the Puppet public GPG key used for package verification.
-   `-d` — Also remove any databases created during installation.
-   `-h` — Display a help message.
-   `-n` — Run in `noop` mode; show commands that would have been run during uninstallation without actually running them.
-   `-y` — Don't ask to confirm uninstallation, assuming an answer of yes.

To remove every trace of PE from a system, run:

```
$ sudo ./puppet-enterprise-uninstaller -d -p
```

