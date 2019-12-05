---
layout: default
title: "Puppet Enterprise quick start guide for *nix users"
canonical: "/pe/latest/quick_start.html"
---

Welcome to the Puppet Enterprise quick start guide. Whether you’re setting up a PE installation for actual deployment or want to learn some fundamentals of configuration management with Puppet Enterprise, this series of guides provides the steps you need to get up and running relatively quickly. We’ll walk you through the setup of a monolithic (all-in-one node) installation and show you how to automate some basic tasks that sysadmins regularly perform.

The following guides present tasks in the order that you would most likely perform them. See the prerequisite sections in each guide to ensure you have the required setup.

### 1. Install a monolithic Puppet Enterprise deployment
Follow [these instructions](./quick_start_install_mono.html) to quickly install a monolithic PE deployment on a *nix machine. A monolithic PE deployment entails installing the Puppet master, the PE console, and PuppetDB all on one node. This guide also reviews prerequisites necessary to a PE installation.

### 2. Install the Puppet agent
Follow [these instructions](./quick_start_install_agents_nix.html) to quickly install a Puppet agent. The Puppet agent regularly pulls configuration catalogs from a Puppet master and applies them locally. These instructions include how to sign the agent certificate request in the console.

### 3. Hello, world!
Follow [these instructions](./quick_start_helloworld.html) to learn the fundamentals of writing a Puppet module. You'll write a very simple module that contains classes to manage your motd (message of the day) and create a Hello, World! notification on the command line.

### 4. Install a module
Follow [these instructions](./quick_start_module_install_nix.html) to install a Puppet module. Modules contain [classes]({{puppet}}/lang_classes.html), which are named chunks of Puppet code and are the primary means by which Puppet Enterprise configures and manages nodes.

### 5. Add classes from the console
Next, you'll add a class to your Puppet agent by following [these instructions](./quick_start_adding_class_nix.html). The class you'll install is derived from the module used in the module installation quick start guide.

### 6. Classifying nodes and managing users
[These instructions](./quick_start_nc_rbac.html) introduce you to node classification and role-based access control. You'll create a new node group and add nodes to it. You'll add classes to a node, a process that's also known as classifying a node. Then you'll create a new user role, and grant that role permission to work with the node group you created.

### 7. Module writing quick start guide
Finally, follow [these instructions](./quick_writing_nix.html) for exercises in writing modules to help you become more familiar with Puppet modules and module development.



