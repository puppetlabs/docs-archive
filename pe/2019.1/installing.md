---
author: melissa amos <melissa.amos@puppet.com\>
---

# Installing

A Puppet Enterprise deployment typically includes infrastructure components and agents, which are installed on nodes in your environment. 

You can install infrastructure components in multiple configurations and scale up with compilers. You can install agents on \*nix, Windows, and macOS nodes. 

-   **[Choosing an architecture](choosing_an_architecture.md)**  
There are several configurations available for Puppet Enterprise. The configuration you use depends on the number of nodes in your environment and the resources required to serve agent catalogs.
-   **[System requirements](system_requirements.md)**  
Refer to these system requirements for Puppet Enterprise installations.
-   **[What gets installed and where?](what_gets_installed_and_where.md#)**  
Puppet Enterprise installs several software components, configuration files, databases, services and users, and log files. It's useful to know the locations of these should you ever need to troubleshoot or manage your infrastructure.
-   **[Installing Puppet Enterprise](installing_pe.md#)**  
You can install PE using express install, which relies on defaults, text install, where you provide a `pe.conf` file with installation parameters, or using a guided web install. Any of these methods is appropriate for installing infrastructure components on your master.
-   **[Purchasing and installing a license key](purchasing_and_installing_a_license_key.md#)**  
Your license must support the number of nodes that you want to manage with Puppet Enterprise.
-   **[Installing agents](installing_agents.md#)**  
You can install Puppet Enterprise agents on \*nix, Windows, and macOS.
-   **[Installing compilers](installing_compile_masters.md#)**  
As your Puppet Enterprise infrastructure scales up to 4,000 nodes and beyond, add load-balanced compilers to your monolithic installation to increase the number of agents you can manage.
-   **[Installing PE client tools](installing_pe_client_tools.md#)**  
PE client tools are a set of command line tools that let you access Puppet Enterprise services from a workstation that might or might not be managed by Puppet.
-   **[Installing external PostgreSQL](installing_postgresql.md#)**  
By default, Puppet Enterprise includes its own database backend, PE-PostgreSQL, which is installed alongside PuppetDB. If the load on your PuppetDB node is larger than it can effectively scale to \(greater than 20,000 nodes\), you can install a standalone instance of PE-PostgreSQL. 
-   **[Uninstalling](uninstalling.md#)**  
Puppet Enterprise includes a script for uninstalling. You can uninstall component infrastructure nodes or from agent nodes.

