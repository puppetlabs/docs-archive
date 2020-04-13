# Create, install and repackage packages with the `chocolatey` module

Chocolatey is a package manager for Windows that is similar in design and execution to package managers on non-Windows systems. The `chocolatey` module is a Puppet Approved Module, so it's not eligible for Puppet Enterprise support services. The module has the capability to intall and configure Chocolatey itself, and then manage software on Windows with Chocolatey packages.

-   **[View existing packages](view_existing_packages.md)**  
Chocolatey has a custom provider for the package resource type, so you can use `puppet resource` to view existing packages.
-   **[Install Chocolatey](install_chocolatey.md)**  
These steps are to install Chocolatey \(choco.exe\) itself. You use the module to ensure Chocolatey is installed.
-   **[Install a package with chocolatey](install_a_package_with_chocolatey.md)**  
Normally, when installing packages you copy them locally first, make any required changes to bring everything they download to an internal location, repackage the package with the edits, and build your own packages to host on your internal package repository \(feed\). For this exercise, however, you directly install a portable Notepad++ from Chocolatey's community feed. The Notepad++ CommandLine package is portable and shouldn't greatly affect an existing system.
-   **[Copy an existing package and make it internal \(repackaging packages\)](repackage_a_package_with_chocolatey.md)**  
To make the existing package local, use these steps.
-   **[Create a package with chocolatey](create_package_with_chocolatey.md)**  
Creating your own packages is, for some system administrators, surprisingly simple compared to other packaging standards.
-   **[Uninstall packages with Chocolatey](uninstall_packages_with_chocolatey.md)**  
In addition to installing and creating packages, Chocolatey can also help you uninstall them.

