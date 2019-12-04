---
layout: default
title: "Puppet Enterprise quick start guide for Windows users"
canonical: "/pe/latest/quick_start_windows_intro.html"

---

Welcome to the Puppet Enterprise (PE) Quick Start Guide for Windows users. This guide demonstrates some essential tasks for getting started with PE, either to set up an initial install for actual deployment, or as a way to try out some fundamentals of configuration management.

We’ll walk you through the setup of a monolithic installation, a PE deployment in which the Puppet master, the PE console, and PuppetDB are all installed on one node. The monolithic install is recommended for those who have a small number of nodes to manage, Or just want to try out a basic PE setup. The various sections of the Quick start guide also show you how to automate some basic tasks that sysadmins regularly perform.

>**Important**: Windows users should be aware that the Puppet master components can currently only be installed on a Linux machine. Puppet agent components can be installed on Windows machines and you can manage those machines with your Puppet master.

<!--Concept-->
## Quick start guide overview

The exercises in this guide build on each other to lay in some foundational configuration tasks. See the prerequisite sections in each guide to ensure you have the correct setup to perform the steps as they're provided.

### 1. Install a monolithic Puppet Enterprise deployment

Quickly install a monolithic PE deployment on a linux machine. A monolithic PE deployment entails installing the Puppet master, the PE console, and PuppetDB all on one node. 

Note that these steps are the same for Windows and *nix users. However, if you plan to access the master *nix machine remotely from a Windows machine, then you should follow the steps to install a master and agent on the [Windows installing page]({{pe}}/windows_installing.html). In that case, complete the master and agent install and then return to the [module installation page]({{pe}}/quick_start_module_install_windows.html) to carry on with this quick start guide.  

### 2. Install the Puppet agent

A computer running the Puppet agent is usually referred to as an “agent node.” The Puppet agent regularly pulls configuration catalogs from a Puppet master and applies them to the local system. In this way, agents maintain the configuration you want.

These instructions include how to sign the agent cert request in the console.

### 3. Install a module

Modules contain [classes]({{puppet}}/lang_classes.html), which are named chunks of Puppet code and are the primary means by which Puppet Enterprise configures and manages nodes. In this section, you install a module from the Puppet Forge, a repository for Puppet modules created by Puppet and by the Puppet community. Modules are shareable, reusable units of Puppet code that extend Puppet across your infrastructure by automating tasks such as setting up a database, web server, or mail server.

### 4. Add classes from the console

Add a class to your Puppet agent. Classes are named chunks of Puppet code that are stored in modules for later use. You assign the class to your agent from the PE console. The class you install in this exercise is derived from the module you installed in the module install QSG. 

### 5. Managing users

Using the PE role-based access control, create a new user role, and grant that role permission to work with the node group you created previously.

### 6. Module Writing Quick Start Guide

Learn about Puppet modules and module development by writing your own basic module.


### Try it yourself
1. [Install PE](./quick_start_install_mono.html)
2. [Install a Puppet agent](./quick_start_install_agents_windows.html)
3. [Install a module](./quick_start_module_install_windows.html)
4. [Add classes to a Puppet agent](./quick_start_adding_class_windows.html)
5. [Assign user access](./quick_start_rbac_windows.html)
6. [Write Windows modules](./quick_writing_windows.html)

