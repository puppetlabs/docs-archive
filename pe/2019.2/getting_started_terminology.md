---
author: Logan Mantyla <logan.mantyla@puppet.com\>
---

# Key components of PE

Here are notes about some the key components within PE that will help you along the way.

## The Puppet master

The Puppet master, or master, is the central hub of activity and process in Puppet Enterprise. This is where PE compiles code to create agent catalogs and where SSL certificates are verified and signed.

## Puppet Server

Puppet Server is an application that runs on the Java Virtual Machine \(JVM\) on the master. In addition to hosting endpoints for the certificate authority service, it also powers the catalog compiler.

## Catalog

To configure a managed node, the agent uses a document called a catalog. The catalog describes the desired state for each resource that should be managed on the node. A resource is a configurable unit of information, like a user or service.

## Agents

Agents are responsible for ensuring your configuration stays in the desired state by periodically checking it against the catalog. If there are changes, the agent sends a report to the master or compiler and changes it back to the desired state based on what the catalog says.

## Role-based access control \(RBAC\)

RBAC is the way you manage user permissions in PE. With RBAC, you define a role within your infrastructure, configure the permissions within the role, and assign users who need those permissions to that role. This way, you do not have to manage individual user permissions.

## Node classifier \(NC\)

PE comes with its own node classifier, which is built into the console. Classification is when you configure your managed nodes by assigning classes to them. **Classes** provide the Puppet code—distributed in modules—that enables you to define the function of a managed node, or apply specific settings and values to it.

## Modules

Modules are self-contained, shareable bundle of code and data. Each module manages a specific task in your infrastructure, like installing and configuring a piece of software. Most Puppet code is written within modules. You can write your own modules or download pre-built modules from the Forge.

## Forge

The [Forge](http://forge.puppet.com) is a Puppet-run repository for storing, sharing, and installing modules. Some modules are approved by Puppet, meaning they have been tested rigorously by Puppet and Puppet maintains them. Other modules are built and submitted by users or organizations.

## Puppet Development Kit \(PDK\)

The [PDK](https://puppet.com/docs/pdk/1.x/pdk.html) is downloadable tool for building modules. It provides a command line interface and integrated testing features to help you develop high-quality code. We highly recommend checking out the PDK when you begin writing your own code.

