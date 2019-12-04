---
layout: default
title: "Essential configuration tasks for Puppet Enterprise users"
canonical: "/pe/latest/quick_start_essential_config.html"
---

The following are common configuration tasks that you can manage with Puppet Enterprise. These steps provide an excellent introduction to the capabilities of PE.

## NTP quick start guide
NTP is one of the most crucial, yet easiest, services to configure and manage with PE. Follow [this guide](./quick_start_ntp.html) to properly get time synced across all your PE-managed nodes.

## DNS quick start guide
[This guide](./quick_start_dns.html) provides instructions for getting started managing a simple DNS nameserver file with PE. A nameserver ensures that the “human-readable” names you type in your browser (for example, `google.com`) can be resolved to IP addresses that computers can read.

## SSH quick start guide
[This guide](./quick_start_ssh.html) provides instructions for getting started managing SSH across your PE deployment using a module from the Puppet Forge.

## Sudo users quick start guide
Managing sudo on your agent nodes allows you to control which system users have access to elevated privileges. [This guide](./quick_start_sudo.html) provides instructions for getting started managing sudo privileges across your nodes, using a module from the Puppet Forge in conjunction with a simple module you will write.

## Firewall quick start guide
Follow the steps in [this guide](./quick_start_firewall.html) to get started managing firewall rules with a simple module you’ll write that defines those rules.