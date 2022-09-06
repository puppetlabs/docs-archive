# Getting started on Windows

Welcome to Puppet Enterprise \(PE\) getting started for Windows users. These pages demonstrate some essential tasks to start using PE, either to help you set up an initial installation for actual deployment, or as a way for you to try out some fundamentals of configuration management.

You'll walk through the setup of a monolithic installation: a PE deployment in which the Puppet master, the PE console, and PuppetDB are all installed on one node. The various sections of the getting started guide show you how to automate some basic tasks that sysadmins regularly perform.

**Important:** For the most part, interacting with Puppet is the same regardless of your operating system. The key difference between other operating systems and Windows is that you cannot configure a Windows machine to be a Puppet master. Agent components can be installed on Windows machines and you can manage those machines with your Linux master.

The exercises build on each other to lay in some foundational configuration tasks. Work through them in the order presented. See the prerequisite sections in each page to ensure you have the correct setup to perform the steps as they're provided.

-   **[Start installing PE in a Windows environment](install_puppet_enterprise_for_windows_getting_started_guide.md#)**  
 This getting started guide uses a web-based installer to walk you through a basic monolithic Puppet Enterprise \(PE\) installation, ideal for trying out PE with up to 10 nodes, or for managing up to 4000 nodes.
-   **[Start installing Windows agents](agent_install_getting_started_guide.md#)**  
When a node is managed by Puppet, it runs a Puppet agent application, commonly called an agent. In this section you'll install a Windows Puppet agent, which regularly pulls configuration catalogs from a Puppet master and applies them locally. These instructions include how to sign the agent certificate request in the console.
-   **[Start installing modules](module_install_getting_started_guide_windows.md#)**  
Modules are shareable, reusable units of Puppet code that extend Puppet across your infrastructure by automating tasks such as setting up a database, web server, or mail server. In this section, you'll install a module from the Puppet Forge.
-   **[Start adding classes](add_classes_gsg.md#)**  
In this section, you'll use the console to add a class to your Puppet agent. Classes are named chunks of Puppet code that are stored in modules. The class you assign in this exercise is derived from the module you installed previously.
-   **[Start assigning user access](assign_user_access_windows_getting_started.md#)**  
The console enables you to import users and groups, create user roles, and assign users to roles. In this exercise, you create a user role, and give the role view permissions on the node group you previously created. Then you create a local user, and assign a user role to that user.
-   **[Start writing modules for Windows](module_writing_windows_getting_started.md#)**  
In this section, you'll learn about Puppet modules and module development by writing your own basic module. You'll create a site module and use the console to apply your new module's class to a group.
-   **[Next Steps](next_steps_windows.md)**  
After you've worked through the Puppet Enterprise \(PE\) getting started steps, you can now perform the core workflows of a Puppet user.

