---
author: Logan Mantyla <logan.mantyla@puppet.com\>
---

# Manage Apache services

Install and manage your Apache server using a module from the Forge.

-   **[Apache overview](apache_overview_pe.md#)**  
Apache, also called the HTTP, is common web server software responsible for bridging web server files with the actual content web users see on the internet, creating a readable website. Since Apache is crucial for a lot of websites to function, it is important to ensure it is installed and configured correctly.
-   **[Install the Apache module](install_forge_module.md#)**  
The Puppet Forge contains thousands of modules that you can use in your own environment.
-   **[Let a node manage Apache by applying a class](complete_basic_classification_workflow.md#)**  
Classification is a way to configure nodes by creating node groups, adding nodes to the groups, and adding classes that specify what types of resources and code can be used by nodes in the group. Allow your agent node to manage Apache by adding the `apache` class to it.
-   **[Determine who can access Apache configurations](complete_basic_rbac_workflow.md#)**  
Role-based access control \(RBAC\) is used to manage user permissions. Permissions define what actions users can perform on designated objects. There are multiple steps involved in an RBAC workflow, which can be adapted to fit your needs.
-   **[Write and modify modules for Apache](writing_modules_nix_getting_started_guide.md#)**  
Modify existing modules in the Forge to better meet your needs. You can also write your own modules, using the Puppet Development Kit to create and unit test it. Work through some examples of modifying and writing modules using your Apache configurations.

