---
title: "PE 2015.2 » Release Notes >> Known Issues"
layout: default
subtitle: "Razor Known Issues"
canonical: "/pe/latest/release_notes_known_issues_razor.html"
---

As we discover them, this page will be updated with known issues related to Razor in Puppet Enterprise 2015.2 releases. Fixed issues will be removed from this page and noted in the Bug Fixes section of the release notes. If you find new problems yourself, please file bugs in [our issue tracker](https://tickets.puppetlabs.com).


## **New:** Authentication fails after Puppet runs
Custom Shiro authentication files saved in the default location, `/etc/puppetlabs/razor-server/shiro.ini`, are replaced by a template version on Puppet runs. This substitution causes authentication to fail. As a workaround, you can specify a custom Shiro file in the `shiro.ini` path of the `config.yaml` file. <!--RAZOR-659-->

## Razor Installation Requires an Internet Connection
The pe_razor module assumes internet connectivity to download the PE installation tarball from pm.puppetlabs.com. The module also assumes it can download the microkernel from pm.puppetlabs.com as well.

## Razor has Trouble Provisioning a Node with Debian Wheezy

We've had errors when trying to provision a node with Debian Wheezy. In such cases, the image hasn't loaded.

## Razor doesn't handle local time jumps

The Razor server is sensitive to large jumps in the local time, like the one that is experienced by a VM after it has been suspended for some time and then resumed. In that case, the server will stop processing background tasks, such as the creation of repos. To remediate that, restart the server with `service pe-razor-server restart`.

## Razor hangs in VirtualBox 4.3.6

We're finding that VirtualBox 4.3.6 gets to the point of downloading the microkernel from the Razor server and hangs at 0% indefinitely. We don't have this problem with VirtualBox 4.2.22.

## Temp Files aren't Removed in a Timely Manner

This is due to Ruby code working as designed, and while it takes longer to remove temporary files than you might expect, the files are eventually removed when the object is finalized.

## Updates might be required for VMware ESXi 5.5 igb files

You might have to update your VMware ESXi 5.5 ISO with updated igb drivers before you can install ESXi with Razor. See this [driver package download page on the VMware site](https://my.vmware.com/web/vmware/details?downloadGroup=DT-ESXI55-INTEL-IGB-42168&productId=353) for the updated igb drivers you need.

## JSON warning

When you run Razor commands, you might get this warning: "MultiJson is using the default adapter (ok_json). We recommend loading a different JSON library to improve performance."

You can disregard the warning since this situation is completely harmless. However, if you're using Ruby 1.8.7, you can install a separate JSON library, such as json_pure, to prevent the warning from appearing.

## `pe-razor` Doesn't Allow `java_args` Configuration

Most PE services enable you to configure `java_args` in the console, but Razor requires you to hard code them in the `init` script.