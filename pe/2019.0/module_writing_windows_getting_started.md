# Start writing modules for Windows

In this section, learn about Puppet modules and module development by writing your own basic module. Create a site module and use the console to apply your new module's class to a group.

**Note:** This guide assumes that you are not using r10k for Code Manager. If you are using r10k, any modules not managed by r10k are destroyed.

## Module basics

Modules are directory trees. Many modules contain more than one directory.

By default, modules are stored in `/etc/puppetlabs/code/environments/production/modules`. If you're working from a Windows machine, the path is, `C:\ProgramData\PuppetLabs\code environments\production/modules`. You can configure this path with the `modulepath` setting in `puppet.conf`.

The manifest directory of the Puppet `wsus_client` module contains the following files:

-   `wsus_client/` \(the module name\)
    -   `manifests/`
        -   `init.pp` \(contains the `wsus_client` class\)
        -   `service.pp` \(defines `wsus_client::service`\)

Every manifest \(`.pp`\) file contains a single class. File names map to class names in a predictable way: `init.pp` contains a class with the same name as the module; `<NAME>.pp` contains a class called `<MODULE NAME>::<NAME>`; and `<NAME>/<OTHER NAME>.pp` contains `<MODULE NAME>::<NAME>::<OTHER NAME>`.

Many modules contain directories other than `manifests`; for simplicity's sake, we do not cover them in this introductory section.

## Write a Puppet module

Puppet modules save time, but at some point most users also need to write their own modules.

### About this task

These instructions assume you have completed all of the preceding sections in Getting started with Puppet Enterprise for Windows users.

### Step 1: Write a class in a module

Follow these steps to create a module with a single class called `critical_policy` that manages a collection of important settings and options in your Windows registry, most notably the legal caption and text users see before the login screen.

#### About this task

The new class has these characteristics:

-   The `registry::value` defined resource type allows you to use Puppet to manage the parent key for a particular value automatically.
-   The `key` parameter specifies the path the key the values must be in.
-   The `value` parameter lists the name of the registry values to manage. This is copied from the resource title if not specified.
-   The `type` parameter determines the type of the registry values. Defaults to 'string'. Valid values are 'string', 'array', 'dword', 'qword', 'binary', or 'expand'.
-   `data` Lists the data inside the registry value.

#### Procedure

1.  On the Puppet master, make sure you're still in the modules directory, `cd /etc/puppetlabs/code/environments/production/modules`, and then run `mkdir -p critical_policy/manifests` to create the new module directory and its manifests directory.

2.  Use your text editor to create and open the `critical_policy/manifests/init.pp` file.

3.  Edit the init.pp file so it contains the following Puppet code, and then save it and exit the editor:

    ```
    class critical_policy {
    
      registry::value { 'Legal notice caption':
        key   => 'HKLM\Software\Microsoft\Windows\CurrentVersion\Policies\System',
        value => 'legalnoticecaption',
        data  => 'Legal Notice',
        }
    
      registry::value { 'Legal notice text':
        key   => 'HKLM\Software\Microsoft\Windows\CurrentVersion\Policies\System',
        value => 'legalnoticetext',
        data  => 'Login constitutes acceptance of the End User Agreement',
        }
      }
    ```


#### Results

For more information about writing classes, refer to the following documentation:

-   To learn how to write resource declarations, conditionals, and classes in a guided tour format, start at the beginning of [Learning Puppet.](https://puppet.com/download-learning-vm)
-   For a complete but succinct guide to the Puppet language's syntax, see the [Puppet language reference.](https://puppet.com/docs/puppet/5.5/lang_summary.html)
-   For complete documentation of the available resource types, see the [type reference](https://puppet.com/docs/puppet/5.5/lang_data_resource_type.html).
-   For short, printable references, see the [modules cheat sheet](https://puppet.com/docs/puppet/5.5/cheatsheet_module.html)and the [core types cheat sheet](https://puppet.com/docs/puppet/5.5/cheatsheet_core_types.html).

### Step 2: Use your custom module in the console

Puppet recognizes when you create a custom class, and it can be added to the console and assigned to your Windows nodes.

#### Procedure

1.  In the console, click **Classification**, and select the node group you want to add your module to \(for example, `Windows_example`\).

2.  On the **Configuration** tab, in the **Add new class** field, enter critical\_policy.

    You might need to wait a moment or two for the class to show up in the list. You can also click `Refresh`.

3.  Click **Add class**, and commit changes.


#### Results

### Step 3: Test out your module

Check to make sure your module does what you expect it to: display the legal caption and text before you log in.

#### Procedure

1.  On the Windows agent node, manually set the data values of `legalnoticecaption` and **legalnoticetext** to some other values. For example, set `legalnoticecaption` to “Larry’s Computer” and set `legalnoticetext` to “This is Larry’s computer.”

2.  On the Windows agent node, refresh the registry and note that the values of `legalnoticecaption` and `legalnoticetext` have been returned to the values specified in your `critical_policy` manifest.

3.  Reboot your Windows machine to see the legal caption and text before you log in again. You have created a new class from scratch and used it to manage registry settings on your Windows server.


### Step 4: Use a site module

Many users create a "site" module for a type of machine.

#### About this task

Instead of describing smaller units of a configuration, the classes in a site module describe a complete configuration for a given type of machine. For example, a site module might contain:

-   A `site::basic` class, for nodes that require security management but haven't been given a specialized role yet.

-   A `site::webserver` class for nodes that serve web content.

-   A `site::dbserver` class for nodes that provide a database server to other applications.


Site modules hide complexity so you can more easily divide labor at your site. System architects can create the site classes, and junior admins can create new machines and assign a single "role" class to them in the console. In this workflow, the console controls policy, not fine-grained implementation.

#### Procedure

1.  On the Puppet master, create the `/etc/puppetlabs/code/environments/production/modules/site/manifests/basic.pp` file by running `mkdir -p site/manifests`. Then, edit it to contain the following:

    ```
    class site::basic {
      if $osfamily == 'windows' {
        include critical_policy
      }
      else {
        include motd
        include core_permissions
      }
    }
    ```

2.  Run `puppet agent -t` to ensure `site::basic` is created. This class declares other classes with the `include` function.

    **Note:** The "if" conditional sets different classes for different operating systems using the `$osfamily` fact. In this example, if an agent node is not a Windows agent, Puppet applies the `motd` and `core_permissions` classes. For more information about declaring classes, see the modules and classes chapters of Learning Puppet.

3.  In the console, remove all of the previous example classes from your nodes and groups \(for example, `wsus_client` and `critical_policy`\). Be sure to leave the `pe_*` classes in place.

4.  Add the `site::basic` class to the console with **Add new class**.

5.  Assign the `site::basic` class to the `windows_example` group. Your nodes are now receiving the same configurations as before, but with a simplified interface in the console. Instead of deciding which classes a new node should receive, you can decide what type of node it is and take advantage of decisions you made earlier.


### Summary

Writing modules enables you to manage your PE configurations.

In this section, you have performed the core workflows of an intermediate Puppet user.

In the course of their normal work, intermediate users:

-   Download and modify Forge modules to fit their deployment's needs.
-   Create new modules and write new classes to manage many types of resources, including files, services, packages, user accounts, and more.
-   Build and curate a site module to safely empower junior admins and simplify the decisions involved in deploying new machines.
-   Monitor and troubleshoot events that affect their infrastructure.

