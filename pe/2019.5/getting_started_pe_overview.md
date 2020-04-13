---
author: Logan Mantyla <logan.mantyla@puppet.com\>
---

# Getting started with Puppet Enterprise

Puppet Enterprise \(PE\) is automation software that helps you and your organization be productive and agile while managing your IT infrastructure.

PE is a commercial version of Puppet, our original open source product used by individuals managing smaller infrastructures. It has all the power and control of Puppet, plus a graphical user interface, orchestration services, role-based access control, reporting, and the capacity to manage thousands of nodes. PE incorporates other Puppet-related tools and products to deliver comprehensive configuration management capabilities.

There are two things you need to get started with PE: your content and the Puppet platform.

## Content

You develop and store your automation content in a Git repository and upload it onto the Puppet platform. It consists of Puppet code, plan and task code, and Hiera data. You store content in a control repo, which contains bundles of code called modules and references to additional content from external sources, like the [Puppet Forge](https://forge.puppet.com/).

## Puppet platform

The Puppet platform includes the master, compilers, and agents. Use it to assign your desired state to managed systems, orchestrate ad-hoc automation tasks on managed and unmanaged systems, and get reports about configuration automation activity.

In this guide, you will learn how to install PE, add nodes to the console, set up your control repo, and run through some basic management tasks using Apache as an example.

Before you begin, check out our video for more information about how Puppet works.



-   **[Install PE](install_pe_getting_started.md#)**  
Use the express install to try out PE with up to 10 nodes or to manage up to 4,000 nodes.
-   **[Add nodes to the inventory](add_inventory.md#)**  
Your inventory is the list of nodes managed by Puppet. Add nodes with agents, agentless nodes that connect over SSH or WinRM, or add network devices like network switches and firewalls. Agent nodes help keep your infrastructure in your desired state. Agentless nodes do not have an agent installed, but can do things like run tasks and plans.
-   **[Add code and set up Code Manager](add_content.md#)**  
Set up your control repo, create a Puppetfile, and configure Code Manager so you can start adding content to your PE environments.
-   **[Add a module](add_modules.md#)**  
Modules are self-contained, shareable bundles of code and data. Each module manages a specific task in your infrastructure, like installing and configuring a piece of software. You can write your own modules or download pre-built modules from the Forge. While you can use any module available on the Forge, PE customers can take advantage of supported modules. These modules are designed to make common services easier, and are tested and maintained by Puppet. A lot of your infrastructure will be supported by modules, so it is important to learn how to install, build, and use them.
-   **[Configure your desired state](configure_desired_state.md#)**  
Tell PE how your infrastructure should be configured by grouping and classifying nodes based on their function. Before you begin, think of which of your inventory nodes you want to configure with Apacheservices.
-   **[Organize webserver configurations with roles and profiles](configure_roles_and_profiles_apache_getting_started.md#)**  
The roles and profiles method is a reliable way to build reusable, configurable, and refactorable system configurations.
-   **[Next steps](next_steps_getting_started_pe.md)**  
Now that you have set up some basic automated configuration management with PE, here are some things to do next:

