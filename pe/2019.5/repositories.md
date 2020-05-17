---
author: melissa amos <melissa.amos@puppet.com\>
---

# Repositories

A repository is where you store all of the actual bits used by Razor to install a node. Or, in some cases, the external location of bits that you link to. A repo is identified by a unique name.

Instructions for the installation, such as what should be installed, where to get it, and how to configure it, are contained in tasks.

To load a repo onto the server, use the command:

```
    razor create-repo --name=<repo name> --task <task name> --iso-url <URL>
```

For example:

```
    razor create-repo --name centos-6.7 --task centos
       --iso-url http://centos.sonn.com/6.7/isos/x86_64/CentOS-6.7-x86_64-bin-DVD1.iso
```

There are three types of repositories that you might want to use, all created with the `create-repo` command:

-   Repos where Razor downloads and unpacks ISOs for you and serves their contents.
-   Repos that are external, such as a mirror that you maintain.
-   Repos where a stub directory is created and you add the contents manually.

The `task` parameter is mandatory for creating all three of these types of repositories, and indicates the default installer to use with this repo. You can override a `task` parameter at the policy level. If you're not using a task, reference the stock task `noop`.

## Unpack an ISO and serve its contents

This repository is created with the `--iso-url` property.

The server downloads and unpacks the ISO image onto its file system:

```
    razor create-repo --name centos-6.7 --task centos
       --iso-url http://centos.sonn.com/6.7/isos/x86_64/CentOS-6.7-x86_64-bin-DVD1.iso
```

## Point to an existing resource

To make a repository that points to an existing resource without loading anything onto the Razor server, provide a `url` property when you create the repository.

The `url` should be serving the unpacked contents of the install media.

```
    razor create-repo --name centos-6.7 --task centos
       --url http://mirror.example.org/centos/6.7/
```

## Create a stub directory

For some install media, especially Windows install DVDs, Razor is not able to automatically unpack the media; this is a known limitation of the library that Razor uses to unpack ISO images.

In those cases, it is necessary to first use `create-repo` to set up a stub directory on the Razor server, then manually add content to it. The stub directory is created with:

```
razor create-repo --name win2012r2 --task windows/2012r2 \
	--no-content true
```

When this command completes successfully, log into your Razor server as root and `cd` into your server's repository directory. The repository directory is specified by the `repo_store_root` class parameter of the `pe_razor` class. By default, the directory is `/opt/puppetlabs/server/data/razor_server/repo`.

```
# mount -o loop /path/to/windows_server_2012_r2.iso /mnt
# cp -pr /mnt/* win2012r2
# umount /mnt
```

**Related information**  


[Create a repository](provisioning_a_nix_node.md#)

[Repository commands](using_the_razor_client.md#)

[Repositories API](api_reference.md#)

