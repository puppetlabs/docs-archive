# Getting started on \*nix

Welcome to Puppet Enterprise \(PE\) getting started for \*nix users. Whether you’re setting up a PE installation for actual deployment or want to learn some fundamentals of configuration management with PE, this series of guides provides the steps you need to get up and running relatively quickly.

We’ll walk you through the setup of a monolithic \(all-in-one node\) installation and show you how to automate some basic tasks that sysadmins regularly perform.

The guides present tasks in the order that you would most likely perform them. See the prerequisite sections in each guide to ensure you have the required setup.

-   **[Start installing PE on \*nix](install_pe_getting_started.md#)**  
This guide uses a web-based installer to walk you through a basic installation. This installation type is ideal for trying out PE with up to 10 nodes, and can be used to manage up to 4000 nodes. This basic setup installs the Puppet master, the PE console, and PuppetDB all on one node, a \*nix machine. This guide also reviews prerequisites necessary to a PE installation.
-   **[Start installing \*nix agents](install_nix_agents_getting_started_guide.md#)**  
When a node is managed by Puppet, it runs a Puppet agent application, commonly called an *agent*. In this guide you'll install a \*nix Puppet agent, which regularly pulls configuration catalogs from a Puppet master and applies them locally. This section includes how to sign the agent certificate request in the console.
-   **[Start installing modules](module_install_getting_started_guide.md#)**  
Modules are self-contained bundles of code and data. You can write your own modules, and you can can download pre-built modules from the Puppet Forge. In this guide, you'll install a Puppet module, the primary means by which PE configures and manages nodes.
-   **[Start adding classes](adding_classes_getting_started_guide.md#)**  
In this guide, you'll create a new node group, then apply a named chunk of Puppet code --- better known as a class --- to the node group. You'll apply the `apache` class to your agent node, which will allow you to launch the default Apache virtual host on your agent node.
-   **[Start assigning user permissions](assigning_user_permissions_getting_started_guide.md#)**  
This section introduces you to role-based access control. You’ll learn how to manage users by creating a user role, granting that role permissions to work with a node group, and assigning users to the role.
-   **[Start writing modules for \*nix](writing_modules_nix_getting_started_guide.md#)**  
In this guide, you'll modify a Forge module, and you'll use the Puppet Development Kit to write a module and unit test it. This will help you become more familiar with Puppet modules and module development.
-   **[Next steps](module_writing_next_steps.md)**  
After you've worked through the Puppet Enterprise \(PE\) getting started steps, you can now perform the core workflows of a Puppet user.

