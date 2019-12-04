---
layout: default
title: "Puppet Enterprise quick start guide for *nix users"
canonical: "/pe/latest/quick_start.html"
---

Welcome to the Puppet Enterprise quick start guide! 

Whether you’re setting up a PE installation for actual deployment or want to learn some fundamentals of configuration management with PE, this series of guides provides the steps you need to get up and running relatively quickly. We’ll walk you through the setup of a monolithic (all-in-one node) installation and show you how to automate some basic tasks that sysadmins regularly perform.

The following guides present tasks in the order that you would most likely perform them. See the prerequisite sections in each guide to ensure you have the required setup.

### 1. Install a monolithic Puppet Enterprise deployment
This basic setup of PE installs the Puppet master, the PE console, and PuppetDB all on one node, a *nix machine. This guide also reviews prerequisites necessary to a PE installation.

### 2. Install the Puppet agent
This guide installs a Puppet agent, which regularly pulls configuration catalogs from a Puppet master and applies them locally. These instructions include how to sign the agent certificate request in the console.

### 3. Install a module
Install a Puppet module, the primary means by which PE configures and manages nodes. Modules contain [classes]({{puppet}}/lang_classes.html), which are named chunks of Puppet code. Thousands of modules are available for use in the [Puppet Forge](https://forge.puppet.com/?_ga=1.60332580.801227828.1463674725). 

### 4. Add classes from the console
Next, you'll add a class to your Puppet agent. The class you'll install is derived from the module used in the module installation quick start guide.

### 5. Classifying nodes and managing users
This section introduces you to node classification and role-based access control. You'll create a new node group and add nodes to it. You'll add classes to a node, a process that's also known as classifying a node. Then you'll create a new user role, and grant that role permission to work with the node group you created.

### 6. Module writing quick start guide
Finally, you write a module to help you become more familiar with Puppet modules and module development.

## Try it yourself
1. [Install PE](./quick_start_install_mono.html) 
2. [Install a Puppet Agent](./quick_start_install_agents_nix.html)
3. [Install a module](./quick_start_module_install_nix.html)
4. [Add classes to a Puppet agent](./quick_start_adding_class_nix.html)
5. [Classify the node and manage users](./quick_start_nc_rbac.html)
6. [Write your own module](./quick_writing_nix.html)





