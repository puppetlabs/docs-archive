---
author: melissa amos <melissa.amos@puppet.com\>
---

# Installing

A Puppet Enterprise deployment typically includes infrastructure components and agents, which are installed on nodes in your environment. 

You can install infrastructure components in multiple configurations, and scale up with compile masters and ActiveMQ hubs and spokes. You can install agents on \*nix, Windows, and macOS nodes, and on certain network devices. 

-   **[Choosing an architecture](choosing_an_architecture.md)**  
There are several configurations available for Puppet Enterprise. The configuration you use depends on the number of nodes in your environment, the resources required to serve your agent catalogs, and your availability requirements.
-   **[System requirements](system_requirements.md)**  
Refer to these system requirements for Puppet Enterprise installations.
-   **[What gets installed and where?](what_gets_installed_and_where.md#)**  
Puppet Enterprise installs several software components, configuration files, databases, services and users, and log files. It's useful to know the locations of these should you ever need to troubleshoot or manage your infrastructure.
-   **[Installing Puppet Enterprise](installing_pe.md#)**  
You can install PE in a monolithic configuration, where all infrastructure components are installed on one node, or in a split configuration, where the master, PuppetDB, and console are installed on separate nodes.
-   **[Purchasing and installing a license key](purchasing_and_installing_a_license_key.md#)**  
You can download and install Puppet Enterprise on up to 10 nodes at no charge, and no license key is needed. When you have 11 or more active nodes and no license key, PE logs license warnings until you install an appropriate license key.
-   **[Installing agents](installing_agents.md#)**  
You can install Puppet Enterprise agents on \*nix, Windows, and macOS.
-   **[Installing network device agents](installing_network_device_agents.md#)**  
Install agents on network switches to operate them with Puppet Enterprise as managed devices.
-   **[Installing compile masters](installing_compile_masters.md#)**  
As your Puppet Enterprise infrastructure scales up to 4,000 nodes and beyond, add load-balanced compile masters to your monolithic installation to increase the number of agents you can manage.
-   **[Installing ActiveMQ hubs and spokes](installing_activemq_hubs_and_spokes.md#)**  
Add hubs and spokes to large Puppet Enterprise deployments for efficient load balancing and for relaying MCollective messages.
-   **[Installing PE client tools](installing_pe_client_tools.md#)**  
PE client tools are a set of command line tools that let you access Puppet Enterprise services from a workstation that is not necessarily managed by Puppet.
-   **[Installing external PostgreSQL](installing_postgresql.md#)**  
By default, Puppet Enterprise includes its own database backend, PE-PostgreSQL, which is installed alongside PuppetDB. If the load on your PuppetDB node is larger than it can effectively scale to \(greater than 20,000 nodes\), you can install a standalone instance of PE-PostgreSQL. 
-   **[Uninstalling](uninstalling.md#)**  
Puppet Enterprise includes a script for uninstalling. You can uninstall component infrastructure nodes or from agent nodes.

