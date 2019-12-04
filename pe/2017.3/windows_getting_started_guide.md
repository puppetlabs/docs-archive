# Windows getting started guide

Welcome to Puppet Enterprise \(PE\) getting started for Windows users. These pages demonstrate some essential tasks to start using PE, either to set up an initial install for actual deployment, or as a way to try out some fundamentals of configuration management.

You'll walk through the setup of a monolithic installation: a PE deployment in which the Puppet master, the PE console, and PuppetDB are all installed on one node. The monolithic install is recommended for those who have a small number of nodes to manage, Or just want to try out a basic PE setup. The various sections of the getting started guide show you how to automate some basic tasks that sysadmins regularly perform.

**Important:** Puppet master components can currently only be installed on a Linux machine. Agent components can be installed on Windows machines and you can manage those machines with your Linux master.

The exercises build on each other to lay in some foundational configuration tasks. Work through them in the order presented. See the prerequisite sections in each page to ensure you have the correct setup to perform the steps as they're provided.

-   **[Getting started: Installing PE for Windows](install_puppet_enterprise_for_windows_getting_started_guide.md#)**  
 This getting started guide uses a web-based installer to walk you through a basic monolithic Puppet Enterprise installation, ideal for trying out PE with up to 10 nodes, or for managing up to 4000 nodes.
-   **[Getting started: Installing Windows agents](agent_install_getting_started_guide.md#)**  
 After installing a PE master, you're ready to install Puppet agents on Windows nodes. The Puppet agent regularly pulls configuration catalogs from a Puppet master and applies them to the local system. In this way, agents maintain the configuration you want. This guide walks you through installing the agent and signing certificates, a process that is different from installing agents on \*nix nodes.
-   **[Getting started: Installing modules](module_install_getting_started_guide_windows.md#)**  
After installing PE and Windows agents, you're ready to install modules. Modules are shareable, reusable units of Puppet code that extend Puppet across your infrastructure by automating tasks such as setting up a database, web server, or mail server. In this section, you'll install a module from the Puppet Forge.
-   **[Getting started: Adding classes](add_classes_gsg.md#)**  
In this exercise, you'll use the console to add a class to your Puppet agent. Classes are named chunks of Puppet code that are stored in modules. The class you assign in this exercise is derived from the module you installed previously.
-   **[Getting started: Assigning user access](assign_user_access_windows_getting_started.md#)**  
After adding classes to your Puppet agent, you can assign user access. The console enables you to import users and groups, create user roles, and assign users to roles. In this exercise, you create a new user role, and give the role view permissions on the node group you previously created. Then you create a new local user, and assign a user role to that user.
-   **[Getting started: Writing modules for Windows](module_writing_windows_getting_started.md#)**  
Now that you have assigned user access, you'll learn about Puppet modules and module development by writing your own basic module. You'll create a site module and use the console to apply your new module's class to a group.

