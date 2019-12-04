---
layout: default
title: "Provisioning with custom facts"
canonical: "/pe/latest/razor_microkernel.html"

---

You can use a microkernel extension to provision nodes based on hardware info or metadata that isn't available by default in Facter.

When configured, all nodes receive the microkernel extension, which contains instructions for reporting custom facts to the Razor server. The custom facts can then be used to tag nodes, apply policies, and provision eligible nodes.

For example, if you want to provision machines based on hardware chassis or rack location -- facts not available by default in Facter -- you could create custom facts for these properties. Then, you can use the custom facts to create associated tags and policies that install the appropriate OS. For a detailed example of using a microkernel extension to report rack location, see [Server locality using Razor and LLDP](https://puppet.com/blog/server-locality-using-razor-and-lldp).

Creating custom facts for use with Razor is similar to creating custom facts for other Puppet uses. For more information about custom facts, see the Facter [custom fact walkthrough]({{facter}}/custom_facts.html).


## How the microkernel extension works

On both new and existing nodes, the microkernel retrieves and unpacks the latest extension file before each checkin.

The content of the extension file is placed in a new, non-persistent directory on the microkernel image. Changes to the directory aren't saved, and the directory is overwritten when a new extension file is available.

During unpacking, the executable bit on files is preserved. Permissions for the files in the extension are irrelevant, because the microkernel extension runs as root.

## Microkernel extension configuration

The Razor microkernel extension is a ZIP file with the default title `mk-extension.zip`.

Depending on the requirements of the custom facts you're using, the extension might include these directories:

| Directory | Contains | Environment variable modified|
|-----------|----------|------------------------------|
|`bin`      | executables | `PATH`    |
|`lib`      | shared libraries | `LD_LIBRARY_PATH` |
|`lib/ruby/facter/fact.rb` | Ruby code | `RUBYLIB` |
|`facts.d`  | [external facts]({{facter}}/custom_facts.html#external-facts) | -- |

**Note**: You can't change environment variables other than `PATH`, `LD_LIBRARY_PATH`, and `RUBYLIB`. For example, static `FACTER_<factname>` environment variables don't work with the microkernel extension.

## Create the microkernel extension

1. Create a ZIP file, as described in [Microkernel extension configuration](#microkernel-extension-configuration), that contains the files required for your custom facts.
2. Place the ZIP file at `/etc/puppetlabs/razor-server` or, if you've changed the default, the location specified by the `microkernel_extension_zip` parameter of the `pe_razor` module.
3. Make sure the ZIP file is readable by the `pe-razor` user. For example, you can use `chmod 444 <FILENAME>` to make the file readable by all users.


## Tips and limitations

* Use relative paths in your applications or facts, or search standard variables to locate content. Don't use absolute paths, because the content of the directory that the microkernel extension is unpacked to is unpredictable.
* To store persistent state, for example if you want to save the result of a web service lookup to avoiding calling the service at each checkin, don't use the location where the zip file is unpacked on the microkernel. Data stored at this location can be lost when the microkernel refreshes. Using `/tmp` is a valid choice, because the data persists in that location as long as the microkernel extension is running.


* * *
