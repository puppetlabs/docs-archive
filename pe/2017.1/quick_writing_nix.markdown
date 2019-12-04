---
layout: default
title: "Writing modules for *nix quick start guide"
canonical: "/pe/latest/quick_writing_nix.html"
---
<!--Overview-->
In this quick start guide, you'll modify a Forge module, and write a simple module of your own. 

**Modules** are reusable chunks of Puppet code, and are the basic building blocks of any PE deployment. Some modules from the [Puppet Forge](https://forge.puppet.com) will precisely fit your needs, but many are *almost* --- but not quite --- what you need. As a PE user, you'll sometimes adapt pre-written modules to suit your deployment's requirements. In other cases, you'll need to write your own modules from scratch. 

<!--Concept-->
## Where are modules kept? 

By default, Puppet keeps modules in `/etc/puppetlabs/code/environments/production/modules`. This includes modules installed by PE, those that you download from the Forge, and those you write yourself. You can configure this path with the [`modulepath`]({{puppet}}/configuration.html#modulepath) setting in `puppet.conf`.

PE also creates another module directory at `/opt/puppetlabs/puppet/modules`. Don't modify anything in this directory, or add modules of your own to it.

<!--Concept-->
## How are modules structured?

Modules are directory trees. For example, you'll find the following files in the puppetlabs-apache module:

- `apache/` (the module name)
    - `manifests/`
        - `init.pp` (contains the `apache` class)
        - `php.pp` (contains the `php` class to install PHP for Apache)
        - `vhost.pp` (contains the Apache virtual hosts class)
    - `templates/`
        - `vhost/`
            - `_file_header.erb` (contains the vhost template, managed by PE)
            
Every manifest (.pp) file contains a single class. File names map to class names in a predictable way: `init.pp` contains a class with the same name as the module, in this case `apache`. `<NAME>.pp` contains a class called `<MODULE NAME>::<NAME>`. `<NAME>/<OTHER NAME>.pp` contains `<MODULE NAME>::<NAME>::<OTHER NAME>`.

Many modules, including Apache, contain directories other than `manifests` and `templates`. For simplicity's sake, we do not cover them in this introductory guide.

>#### FAQs
>
> * [Where can I learn more about how modules are structured?]({{puppet}}/modules_fundamentals.html) 

<!--Task-->
### Edit a module's manifest and use the edited module

In this simplified exercise, you'll modify a template from the puppetlabs-apache module, specifically `'vhost.conf.erb`, to include some simple variables that will be automatically populated with facts about your node.

Before you begin, make sure you have installed: 
* A [monolithic PE deployment](./quick_start_install_mono.html)
* At least one [*nix agent node](./quick_start_install_agents_nix.html)
* The [puppetlabs-apache module](./quick_start_module_install_nix.html) 

> **Note:** You should be logged in as root or administrator on your nodes.

1. **On the Puppet master,** navigate to the modules directory by running `cd /etc/puppetlabs/code/environments/production/modules`.
1. Run `ls` to view the currently installed modules and note that `apache` is present.
1. Open `apache/templates/vhost/_file_header.erb` in a text editor. (Avoid using Notepad because it can introduce errors.)
      The `_file_header.erb` file contains the following header:

        # ************************************
        # Vhost template in module puppetlabs-apache
        # Managed by Puppet
        # ************************************

1. Use PE's lookup tool, Facter, to collect the following facts about your agent node:
   - run `facter operatingsystem` (this returns your agent node's OS)
   - run `facter id` (this returns the id of the currently logged in user)
1. Edit the header of `_file_header.erb` so that it contains the following variables for Facter lookups:

        # ************************************
        # Vhost template in module puppetlabs-apache
        # Managed by Puppet
        #
        # This file is authorized for deployment by <%= scope.lookupvar('::id') %>.
        #
        # This file is authorized for deployment ONLY on <%= scope.lookupvar('::operatingsystem') %> <%= scope.lookupvar('::operatingsystemmajrelease')     %>.
        #
        # Deployment by any other user or on any other system is strictly prohibited.
        # ************************************

1. **In the console**, add `apache` to the available classes, and then add that class to your agent node. Refer to the [Adding classes quick start guide](./quick_start_adding_class_nix.html) if you need help with these steps.

   When Puppet runs, it configures Apache and starts the httpd service. When this happens, a default Apache vhost is created based on the contents of `_file_header.erb`.

1. **On the agent node**, navigate to one of the following locations, depending on your operating system:
   - Redhat-based: `/etc/httpd/conf.d`
   - Debian-based: `/etc/apache2/sites-available`

1. View `15-default.conf`; depending on the node's OS, the header shows some variation of the following contents:

        # ************************************
        # Vhost template in module puppetlabs-apache
        # Managed by Puppet
        #
        # This file is authorized for deployment by root.
        #
        # This file is authorized for deployment ONLY on Redhat 6.
        #
        # Deployment by any other user or on any other system is strictly prohibited.
        # ************************************

As you can see, PE has used Facter to retrieve some key facts about your node, and then used those facts to populate the header of your vhost template.

<!--Task-->
## Write a new module

In this exercise, you will create a class called `pe_quickstart_app` that manages a PHP-based web app running on an Apache virtual host.

Before you begin, make sure you have installed: 
* A [monolithic PE deployment](./quick_start_install_mono.html)
* At least one [*nix agent node](./quick_start_install_agents_nix.html)
* The [puppetlabs-apache module](./quick_start_module_install_nix.html) 

> **Note:** You should be logged in as root or administrator on your nodes.

1. **On the Puppet master**, make sure you're still in the modules directory:`cd /etc/puppetlabs/code/environments/production/modules`. Then run `mkdir -p pe_quickstart_app/manifests` to create the new module's directory and its manifests directory.
2. Use your text editor to create and open the `pe_quickstart_app/manifests/init.pp` file.
3. Edit the `init.pp` file so it contains the following Puppet code, and then save it and exit the editor:

        class pe_quickstart_app {

          class { 'apache':
            mpm_module => 'prefork',
          }

          include apache::mod::php

          apache::vhost { 'pe_quickstart_app':
            port     => '80',
            docroot  => '/var/www/pe_quickstart_app',
            priority => '10',
          }

          file { '/var/www/pe_quickstart_app/index.php':
            ensure  => file,
            content => "<?php phpinfo() ?>\n",
            mode    => '0644',
          }

        }

   You have written a new module called `pe_quickstart_app` containing a new class that includes two other classes. 

   Additional details about your new class:

   * The class `apache` has been modified to include the `mpm_module` attribute; this attribute determines which multi-process module is configured and loaded for the Apache (HTTPD) process. In this case, the value is set to `prefork`.
   * `include apache::mod::php` indicates that your new class relies on those classes to function correctly. However, PE understands that your node needs to be classified with these classes and completes that work automatically when you classify your node with the `pe_quickstart_app` class; in other words, you don't need to worry about classifying your nodes with Apache and Apache PHP.
   * The `priority` attribute of `10` ensures that your app has a higher priority on port 80 than the default Apache vhost app.
   * The file `/var/pe_quickstart_app/index.php` contains whatever is specified by the `content` attribute. This is the content you see when you launch your app. PE uses the `ensure` attribute to create that file the first time the class is applied.

4. Using the workflow from the
[Adding classes quick start guide](./quick_start_adding_class_nix.html), add your new class to the console and assign it to nodes.

Congratulations! Your first module is up and running. There are plenty of additional resources about modules and the creation of modules that you can reference. Check out [Puppet: Module fundamentals]({{puppet}}/modules_fundamentals.html), [Puppet: The modulepath]({{puppet}}/dirs_modulepath.html), the [Beginner's guide to modules](/guides/module_guides/bgtm.html), and the [Puppet Forge](https://forge.puppet.com).

<!--Reference-->
## More information about writing classes
For more information about writing classes, refer to the following documentation:

   * For a complete but succinct guide to the Puppet language's syntax, including help learning how to write [resource declarations]({{puppet}}/lang_resources.html), [conditionals]({{puppet}}/lang_conditional.html), and [classes]({{puppet}}/lang_classes.html) in a guided tour format, see the [Puppet language reference]({{puppet}}/lang_summary.html).
   * For complete documentation of the available resource types, [see the type reference]({{puppet}}/type.html).
   * For short, printable references, see [the modules cheat sheet](/module_cheat_sheet.pdf) and [the core types cheat sheet](/puppet_core_types_cheatsheet.pdf).

## Next steps

You've reached the end of the PE *nix quick start guide series, and can now perform the core workflows of a Puppet user. We recommend the following next steps:

* [Learn more about working with the Puppet Forge to find the modules you need]({{puppet}}/modules_fundamentals.html)
* [Use the roles and profiles method to create a complete system configuration and store it in a control repository](./r_n_p_intro.html)
* [Manage Puppet code using PE’s built-in tools and a control repository](./cmgmt_managing_code.html)
* [Classify nodes using groups and rules](./console_classes_groups_getting_started.html) 









