# Using the Windows module pack

This guide covers creating a managed permission with `ACL`, creating managed registry keys and values with `registry`, and installing and creating your own packages with `chocolatey`.

-   **[Windows module pack](windows_module_pack.md)**  
The Windows module pack is a group of modules available on the Forge curated to help you complete common Windows tasks.
-   **[Install the Windows module pack](install_windows_module_pack.md)**  
These steps show you how to install the module pack locally, but you can also install it on the master and pluginsync pushes the module pack to all of your nodes.
-   **[Manage permissions with acl](manage_permissions_with_acl.md)**  
The `puppetlabs-acl` module helps you manage access control lists \(ACLs\), which provide a way to interact with permissions for the Windows file system. This module enables you to set basic permissions up to very advanced permissions using SIDs \(Security Identifiers\) with an access mask, inheritance, and propagation strategies. First, start with querying some existing permissions.
-   **[Create Puppet managed registry keys with registry](create_puppet_managed_registry_keys.md)**  
You might eventually need to use the registry to access and set highly available settings, among other things. The `puppetlabs-registry` module, which is also a Puppet Supported Module enables you to set both registry keys and values.
-   **[Create, install and repackage packages with the chocolatey module](create_install_repackage_with_chocolatey.md)**  
Chocolatey is a package manager for Windows that is similar in design and execution to package managers on non-Windows systems. The `chocolatey` module is a Puppet Approved Module, so it's not eligible for Puppet Enterprise support services. The module has the capability to intall and configure Chocolatey itself, and then manage software on Windows with Chocolatey packages.

**Parent topic:**[Manage your Windows infrastructure](manage_windows_infrastructure.md)

