---
layout: default
title: "Bare-metal provisioning with Razor"
canonical: "/pe/latest/razor_intro.html"

---
[razor-1]: ./images/razor/razor-1.png
[razor-2]: ./images/razor/razor-2.png
[razor-3]: ./images/razor/razor-3.png
[razor-4]: ./images/razor/razor-4.png
[razor-5]: ./images/razor/razor-5.png

Razor is a provisioning application that deploys bare-metal systems. Policy-based provisioning lets you use characteristics of the hardware as well as user-provided data to make provisioning decisions. You can automatically discover bare-metal hardware, dynamically configure operating systems and hypervisors, and hand off nodes to PE for workload configuration.

Automated provisioning makes Razor ideal for big installation jobs, like setting up a new selection of servers in a server farm. You can also use Razor to regularly wipe and re-provision test machines.

>**Note**: If you're using Razor in a brownfield environment, review how to [protect existing nodes](./razor_brownfield.html) before you begin.

Provisioning with Razor involves these steps:

+ [Setting up a Razor environment](./razor_prereqs.html)
+ [Installing Razor](./razor_install.html)
+ [Provisioning a node](./razor_using.html)

## How Razor works
The following steps provide a high-level view of the process for provisioning a node with Razor.

### Razor identifies a new node

![bare node][razor-1]

When a new node appears, Razor discovers its characteristics by booting it with the Razor microkernel and using Facter to inventory its facts.

### The node is tagged

![tags applied][razor-2]

The node is tagged based on its characteristics. Tags contain a match condition &#8212; a Boolean expression that has access to the node's facts and determines whether the tag should be applied to the node or not.

### The node tags match a Razor policy

![tags compared to policies][razor-3]

Node tags are compared to tags in the policy table. The first policy with tags that match the node's tags is applied to the node.

### Policies pull together all the provisioning elements

![policies][razor-4]


### Razor provisions the node

![policy applied][razor-5]

The node is now installed. If you choose, you can hand off management of the node to Puppet Enterprise.

## Razor system requirements

The Razor server has been validated on the RHEL/CentOS 6.x and 7.x operating systems.

The Razor client is supported on these operating systems:

* Windows 2008 R2 Server
* Windows 2012 R2 Server
* Windows 2016 Server
* ESXi 5.5
* RHEL 6 & 7
* CentOS 6 & 7
* Ubuntu 14.04
* Debian Wheezy

## Prerequisites for machines provisioned with Razor

To successfully install an operating system on a machine using Razor, the machine must:

* Have at least 512MB RAM.
* Be supported by the operating system that you're installing.
* Be able to successfully boot into the microkernel. The microkernel is based on [CentOS 7](http://wiki.centos.org/Manuals/ReleaseNotes/CentOS7), 64-bit only, and supports the [x86-64 Intel architecture](http://en.wikipedia.org/wiki/X86-64).
* Be able to successfully boot the [iPXE](http://ipxe.org/) firmware.


 * * *

