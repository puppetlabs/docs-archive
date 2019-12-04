---
layout: default
title: "Puppet Enterprise quick start guide for *nix users"
canonical: "/pe/latest/quick_start.html"
---
<!--Overview-->
Welcome to the Puppet Enterprise quick start guide! 

Whether you’re setting up a PE installation for actual deployment or want to learn some fundamentals of configuration management with PE, this series of guides provides the steps you need to get up and running relatively quickly. We’ll walk you through the setup of a monolithic (all-in-one node) installation and show you how to automate some basic tasks that sysadmins regularly perform.

<!--Concept-->
## Introducing the *nix quick start guides

The following guides present tasks in the order that you would most likely perform them. See the prerequisite sections in each guide to ensure you have the required setup.

### 1. Install a monolithic Puppet Enterprise deployment
This basic setup of PE installs the Puppet master, the PE console, and PuppetDB all on one node, a *nix machine. This guide also reviews prerequisites necessary to a PE installation.

### 2. Install the Puppet agent
This guide installs a Puppet agent, which regularly pulls configuration catalogs from a Puppet master and applies them locally. These instructions include how to sign the agent certificate request in the console.

### 3. Install a module
This guide installs a Puppet module, the primary means by which PE configures and manages nodes. Thousands of modules are available for use on the [Puppet Forge](https://forge.puppet.com). 

### 4. Add classes
This guide creates a new node group, then applies a named chunk of Puppet code --- better known as a [class]({{puppet}}/lang_classes.html) --- to the node group. 

### 5. Give users access
This section introduces you to role-based access control. You’ll create a new user role, and grant that role permission to work with the node group you created previously.

### 6. Write a module
Finally, you'll write a module to help you become more familiar with Puppet modules and module development.

### Try it yourself
1. [Install PE](./quick_start_install_mono.html) 
2. [Install a Puppet Agent](./quick_start_install_agents_nix.html)
3. [Install a module](./quick_start_module_install_nix.html)
4. [Add classes](./quick_start_adding_class_nix.html)
5. [Give users access](./quick_start_nc_rbac.html)
6. [Write your own module](./quick_writing_nix.html)

### Next steps

Once you’ve completed the quick start guide series, we recommend the following next steps:

* [Learn more about working with the Puppet Forge to find the modules you need]({{puppet}}/modules_fundamentals.html)
* [Use the roles and profiles method to create a complete system configuration and store it in a control repository](./r_n_p_intro.html)
* [Manage Puppet code using PE’s built-in tools and a control repository](./cmgmt_managing_code.html)
* [Classify nodes using groups and rules](./console_classes_groups_getting_started.html)




