---
layout: default
title: "Writing modules for *nix quick start guide"
canonical: "/pe/latest/quick_writing_nix.html"
---

Welcome to the writing modules exercise in the Puppet Enterprise quick start guide series. This document is a short walk-through of Puppet modules, module development, and additional PE features. Follow along to learn how to:

* Modify a module obtained from the Forge
* Write your own Puppet module

> Before starting this walk-through, complete the previous exercises in the [introductory quick start guide](./quick_start.html). You should still be logged in as root or administrator on your nodes.

## Getting started

In the [Apache module installation QSG](./quick_start_module_install_nix.html), you installed the latest version of the puppetlabs-apache module.

If you do not have this installed, install it by running `puppet module install puppetlabs-apache`.

## Editing a Forge module

Although many Forge modules are exact solutions that fit your site, many are *almost* but not quite what you need. Sometimes you need to edit a Forge module.

### Module basics

By default, modules are stored in `/etc/puppetlabs/code/environments/production/modules`. You can configure this path with the [`modulepath`]({{puppet}}/configuration.html#modulepath) setting in `puppet.conf`.)

Modules are directory trees. For these exercises you'll use the following files:

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

* For more on how modules work, see [Module fundamentals]({{puppet}}/modules_fundamentals.html) in the Puppet documentation.
* For more on best practices, methods, and approaches to writing modules, see the [Beginner's guide to modules](/guides/module_guides/bgtm.html).


### Editing a manifest

In this simplified exercise, you'll modify a template from the Puppet Apache module, specifically `'vhost.conf.erb`, to include some simple variables that will be populated by facts (using PE's implementation of Facter) about your node.

1. **On the Puppet master,** navigate to the modules directory by running `cd /etc/puppetlabs/code/environments/production/modules`.
2. Run `ls` to view the currently installed modules and note that `apache` is present.
3. Open `apache/templates/vhost/_file_header.erb` in a text editor. Avoid using Notepad because it can introduce errors.
      The `_file_header.erb` file contains the following header:

        # ************************************
        # Vhost template in module puppetlabs-apache
        # Managed by Puppet
        # ************************************

4. Collect the following facts about your agent node:
   - run `facter osfamily` (this returns your agent node's OS)
   - run `facter id` (this returns the id of the currently logged in user)
5. Edit the header of `_file_header.erb` so that it contains the following variables for Facter lookups:

        # ************************************
        # Vhost template in module puppetlabs-apache
        # Managed by Puppet
        #
        # This file is authorized for deployment by <%= scope.lookupvar('::id') %>.
        #
        # This file is authorized for deployment ONLY on <%= scope.lookupvar('::osfamily') %> <%= scope.lookupvar('::operatingsystemmajrelease')     %>.
        #
        # Deployment by any other user or on any other system is strictly prohibited.
        # ************************************

6. **In the console**, add `apache` to the available classes, and then add that class to your agent node. Refer to the [Adding classes quick start guide](./quick_start_adding_class_nix.html) if you need help with these steps.
7. When Puppet runs it configures apache and starts the httpd service. When this happens, a default apache vhost is created based on the contents of `_file_header.erb`.

8. **On the agent node**, navigate to one of the following locations, depending on your operating system:
   - Redhat-based: `/etc/httpd/conf.d`
   - Debian-based: `/etc/apache2/sites-available`

9. View `15-default.conf`; depending on the node's OS, the header shows some variation of the following contents:

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

But now, let's see what happens you write your own Puppet code.

## Writing a Puppet module

Puppet modules save time, but at some point you might need to write your own modules.

### Writing a class in a module

During this exercise, you will create a class called `pe_quickstart_app` that manages a PHP-based web app running on an Apache virtual host.

1. **On the Puppet master**, make sure you're still in the modules directory:`cd /etc/puppetlabs/code/environments/production/modules`. Then run `mkdir -p pe_quickstart_app/manifests` to create the new module directory and its manifests directory.
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

> You have written a new module `pe_quickstart_app` containing a new class that includes two other classes. Puppet now knows about your new class, and it can be added to the console and assigned to nodes.
>
> Note the following about your new class:
>
> * The class `apache` has been modified to include the `mpm_module` attribute; this attribute determines which multi-process module is configured and loaded for the Apache (HTTPD) process. In this case, the value is set to `prefork`.
> * `include apache::mod::php` indicates that your new class relies on those classes to function correctly. However, PE understands that your node needs to be classified with these classes and completes that work automatically when you classify your node with the `pe_quickstart_app` class; in other words, you don't need to worry about classifying your nodes with Apache and Apache PHP.
> * The `priority` attribute of `10` ensures that your app has a higher priority on port 80 than the default Apache vhost app.
> * The file `/var/pe_quickstart_app/index.php` contains whatever is specified by the `content` attribute. This is the content you see when you launch your app. PE uses the `ensure` attribute to create that file the first time the class is applied.

For more information about writing classes, refer to the following documentation:

* For a complete but succinct guide to the Puppet language's syntax, including help learning how to write [resource declarations]({{puppet}}/lang_resources.html), [conditionals]({{puppet}}/lang_conditional.html), and [classes]({{puppet}}/lang_classes.html) in a guided tour format, see the [Puppet 4.4 language reference]({{puppet}}/lang_summary.html).
* For complete documentation of the available resource types, [see the type reference]({{puppet}}/type.html).
* For short, printable references, see [the modules cheat sheet](/module_cheat_sheet.pdf) and [the core types cheat sheet](/puppet_core_types_cheatsheet.pdf).



