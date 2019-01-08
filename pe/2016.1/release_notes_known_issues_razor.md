---
layout: default
title: "Razor known issues"
canonical: "/pe/latest/release_notes_known_issues_razor.html"
---

This page lists known issues related to Razor in Puppet Enterprise 2016.1.

You can file bugs for issues you identify in [our issue tracker](https://tickets.puppetlabs.com).


## **New:** Authentication fails after Puppet runs

Custom Shiro authentication files saved in the default location, `/etc/puppetlabs/razor-server/shiro.ini`, are replaced by a template version on Puppet runs. This substitution causes authentication to fail. As a workaround, you can specify a custom Shiro file in the `shiro.ini` path of the `config.yaml` file. <!--RAZOR-659-->

## Image doesn't load when provisioning a node with Debian Wheezy

We've had errors when trying to provision a node with Debian Wheezy. In such cases, the image hasn't loaded.

## Temp files aren't removed in a timely manner

This is due to Ruby code working as designed, and while it takes longer to remove temporary files than you might expect, the files are eventually removed when the object is finalized.

## Updates might be required for VMware ESXi 5.5 igb files

You might have to update your VMware ESXi 5.5 ISO with updated igb drivers before you can install ESXi with Razor. See this [driver package download page on the VMware site](https://my.vmware.com/web/vmware/details?downloadGroup=DT-ESXI55-INTEL-IGB-42168&productId=353) for the updated igb drivers you need.

## Razor commands resulted in JSON warning

When you run Razor commands, you might get this warning: "MultiJson is using the default adapter (ok_json). We recommend loading a different JSON library to improve performance."

You can disregard the warning since this situation is completely harmless. However, if you're using Ruby 1.8.7, you can install a separate JSON library, such as json_pure, to prevent the warning from appearing.

## `pe-razor` doesn't allow `java_args` configuration

Most PE services enable you to configure `java_args` in the console, but Razor requires you to hard code them in the `init` script.