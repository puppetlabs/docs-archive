---
author: claire.cadman@puppet.com
---

# **Install and administer Puppet Enterprise on Windows**

Whether you’re setting up a PE installation for actual deployment or want to learn some fundamentals of configuration management with PE, we'll provide you with the steps you need to get up and running relatively quickly.

The steps and concepts are organized in the order that you would most likely perform or encounter them.

-   **[Start installing PE in a Windows environment](install_puppet_enterprise_for_windows_getting_started_guide.md#)**  
 The express install of PE is ideal for trying out PE with up to 10 nodes and can be used to manage up to 4,000 nodes.
-   **[Start installing Windows agents](agent_install_getting_started_guide.md#)**  
When a node is managed by Puppet, it runs a Puppet agent application, commonly called an agent. In this section you install a Windows Puppet agent, which regularly pulls configuration catalogs from a Puppet master and applies them locally. These instructions include how to sign the agent certificate request in the console.
-   **[Start installing modules](module_install_getting_started_guide_windows.md#)**  
Modules are shareable, reusable units of Puppet code that extend Puppet across your infrastructure by automating tasks such as setting up a database, web server, or mail server. In this section, you install a module from the Puppet Forge.
-   **[Start adding classes](add_classes_gsg.md#)**  
In this section, you use the console to add a class to your Puppet agent. Classes are named chunks of Puppet code that are stored in modules. The class you assign in this exercise is derived from the module you installed previously.
-   **[Start assigning user access](assign_user_access_windows_getting_started.md#)**  
The console enables you to import users and groups, create user roles, and assign users to roles. In this exercise, you create a user role, and give the role view permissions on the node group you previously created. Then you create a local user, and assign a user role to that user.
-   **[Start writing modules for Windows](module_writing_windows_getting_started.md#)**  
In this section, learn about Puppet modules and module development by writing your own basic module. Create a site module and use the console to apply your new module's class to a group.

