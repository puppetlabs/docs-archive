# Start writing modules for \*nix

In this guide, you modify a Forge module, and use the Puppet Development Kit to write a module and unit test it. This helps you become more familiar with Puppet modules and module development.

Modules are reusable chunks of Puppet code and are the basic building blocks of any Puppet Enterprise \(PE\) deployment. Some modules from the Forge might precisely fit your needs, but many are *almost* --- but not quite --- what you need. You might want to adapt pre-written modules to suit your deployment's requirements. In other cases, you might need to write your own modules from scratch.

CAUTION:

The directories and some of the commands in this guide do not work correctly with Code Manager. If you are using Code Manager, do not follow the steps in this guide. 

## Module location

By default, Puppet keeps modules in `/etc/puppetlabs/code/environments/production/modules`. This includes modules installed by PE, those that you download from the Forge, and those you write yourself. You can configure this path with the `[modulepath](https://docs.puppet.com/puppet/5.3/configuration.html#modulepath)` setting in `puppet.conf`.

**Note:** PE also creates another module directory at `/opt/puppetlabs/puppet/modules`. Don't modify anything in this directory or add modules of your own to it.

## Module structure

Modules are directory trees that contain manifests, templates, and other files.

These files are in the `puppetlabs-apache` module:

-   `apache/` \(the module name\)
    -   `manifests/`
        -   `init.pp` \(contains the `apache` class\)
        -   `php.pp` \(contains the `php` class to install PHP for Apache\)
        -   `vhost.pp` \(contains the Apache virtual hosts class\)
    -   `templates/`
        -   `vhost/`
            -   `_file_header.erb` \(contains the vhost template, managed by PE\)

Every manifest \(.pp\) file contains a single class. File names map to class names in a predictable way: `init.pp` contains a class with the same name as the module, in this case `apache`. `<NAME>.pp` contains a class called `<MODULE NAME>::<NAME>`. `<NAME>/<OTHER NAME>.pp` contains `<MODULE NAME>::<NAME>::<OTHER NAME>`.

Many modules, including Apache, contain directories other than `manifests` and `templates`. For simplicity's sake, we do not cover them in this introductory guide.

## Edit a module's manifest and use the edited module

Modules from the Forge provide a great basis for common tasks. You can configure them to meet your specific needs.

### Before you begin

These instructions assume you've installed PE, at least one \*nix agent node, and the `puppetlabs-apache` module.

### About this task

In this simplified exercise, you modify a template from the puppetlabs-apache module, specifically `vhost.conf.erb`, to include some simple variables automatically populated with facts about your node.

**Note:** You should be logged in as root or administrator on your nodes.

### Procedure

1.  On the Puppet master, navigate to the modules directory by running `cd /etc/puppetlabs/code/environments/production/modules`

2.  Run `ls` to view the currently installed modules and note that `apache` is present. 

3.  Open `apache/templates/vhost/_file_header.erb` in a text editor. \(Avoid using Notepad because it can introduce errors.\) The `_file_header.erb` file contains the following header:

    ```
    # ************************************
    # Vhost template in module puppetlabs-apache
    # Managed by Puppet
    # ************************************
    ```

4.  Use the PE lookup tool, Facter, to collect the following facts about your agent node:

    -   run `facter operatingsystem` \(this returns your agent node’s OS\)

    -   run `facter id` \(this returns the id of the currently logged in user\)

5.  Edit the header of `_file_header.erb` so that it contains the following variables for Facter lookups:

    ```
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
    ```

6.  In the console, add `apache` to the available classes, and then add that class to your agent node. Refer to the Adding classes getting started guide if you need help with these steps.

    When Puppet runs, it configures Apache and starts the httpd service. When this happens, a default Apache vhost is created based on the contents of `_file_header.erb`.

7.  On the agent node, navigate to one of the following locations, depending on your operating system:

    -   Redhat-based: `/etc/httpd/conf.d`

    -   Debian-based: `/etc/apache2/sites-available`

8.  View `15-default.conf`; depending on the node’s OS, the header shows some variation of the following contents:

    ```
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
    ```


### Results

As you can see, PE has used Facter to retrieve some key facts about your node, and then used those facts to populate the header of your vhost template.

**Related information**  


[Start adding classes](add_classes_gsg.md#)

## Write a new module

Write, validate, and test an example module for PE on \*nix systems with Puppet Development Kit \(PDK\).

### Before you begin

Make sure you've installed:

-   PE
-   At least one \*nix agent node
-   The puppetlabs-apache module
-   Puppet Development Kit on your master. See PDK [installation](https://docs.puppet.com/pdk/latest/pdk_install.html) instructions.


You must be logged in as root or administrator on your nodes.

**Tip:** PDK is a standalone development kit that doesn't require Puppet on your development machine. For simplicity in this example, you create your module on your master, but normally, you would develop modules on a separate development workstation and then move your module to the master.

### About this task

Create a `pe_getstarted_app` module that manages a PHP-based web app running on an Apache virtual host.

### Procedure

1.  Make sure you’re in the modules directory by running `cd /etc/puppetlabs/code/environments/production/modules`

2.  On the master, from the command line, run `pdk new module pe_getstarted_app --skip-interview`

    By default, PDK asks a series of metadata questions before it creates your module. The `--skip-interview` option bypasses the interview, and PDK uses default values for the module's metadata.

    PDK creates the new module’s directory with the same name you gave the module. It also creates the module's metadata, subdirectories, and testing template files.

3.  Change into the module directory by running `cd pe_getstarted_app`

4.  Validate your module's code syntax and style by running `pdk validate`

    The `pdk validate` command checks the Puppet and Ruby code style and syntax in your module. Validate every time you add new code.

5.  Unit test your module by running `pdk test unit` to ensure that the testing directories and templates were correctly created.

    On a newly generated module, the `pdk test unit` command checks that the testing directories and templates were correctly created. As you add new code to your module, write and run unit tests to make sure your code works.


### Create a module class

Write, test, and deploy a module class for an example module.

#### Before you begin

Make sure you have generated the `pe_getstarted_app` module as described in the [Write a module](writing_modules_nix_getting_started_guide.md#) topic.

#### Procedure

1.  In the `pe_getstarted_ap`p directory, create the main class for your module by running `pdk new class pe_getstarted_app`

    When you create a class with the same name as your module, PDK creates the `init.pp` file. This file contains the main class of a module, and it's the only class with a file name that's different from the class name. PDK also creates testing directories and templates for the class.

2.  Validate your class's code syntax and style by running `pdk validate`

3.  Unit test your class by running `pdk test unit`

4.  Open the `init.pp` file in your text editor and add the following Puppet code to it. Save the file and exit the editor.

    ```
    class pe_getstarted_app (
      $content = "<?php phpinfo() ?>\n",
    ) {
    
       class { 'apache':
         mpm_module => 'prefork',
       }
    
       include apache::mod::php
    
       apache::vhost { 'pe_getstarted_app':
         port     => '80',
         docroot  => '/var/www/pe_getstarted_app',
         priority => '10',
       }
    
       file { '/var/www/pe_getstarted_app/index.php':
         ensure  => file,
         content => $content,
         mode    => '0644',
       }
    
     }
    ```

    Additional details about the code in your new class:

    -   The class `apache` is modified to include the `mpm_module` attribute. This attribute determines which multi-process module is configured and loaded for the Apache \(HTTPD\) process. In this case, the value is set to 'prefork'.

    -   `include apache::mod::php` indicates that your new class relies on those classes to function correctly. PE understands that your node needs to be classified with these classes and completes that work automatically when you classify your node with the `pe_getstarted_app` class; in other words, you don’t need to worry about classifying your nodes with Apache and Apache PHP.

    -   The priority attribute of '10' ensures that your app has a higher priority on port 80 than the default Apache vhost app.

    -   The file`/var/pe_getstarted_app/index.php` contains whatever is specified in the `content` attribute. This is the content you see when you launch your app. PE uses the `ensure` attribute to create that file the first time the class is applied.


#### What to do next

After creating your class, you are ready to validate and unit test it.

### Validate and unit test a class

Validate and unit test a class in an example module.

#### About this task

When you add code to a module, always validate and unit test it. PDK creates unit test directories and templates, and then you write the unit tests you need. For this example, we've provided an rspec unit test. To learn more about how to write unit tests, see [rspec-puppet](http://rspec-puppet.com/tutorial/) documentation.

#### Procedure

1.  In the `pe_getstarted_ap`p directory, create a `.fixtures.yml` file. This file installs module dependencies for unit testing.

2.  Open the `.fixtures.yml`file and edit it to include the following code. Save the file and exit the editor.

    ```
    fixtures:
      forge_modules:
         apache: "puppetlabs/apache"
         stdlib: "puppetlabs/stdlib"
         concat: "puppetlabs/concat"
    ```

3.  Change into the spec tests directory by running `cd spec/classes`.

4.  Open the `pe_getstarted_app_spec.rb` in your text editor and add the following code. Save and exit the file.

    ```
    require 'spec_helper'
    
    describe 'pe_getstarted_app', type: :class do
      let(:facts) do
       {
         operatingsystemrelease: '14.04', 
         osfamily: 'Debian',
         operatingsystem: 'Ubuntu',
         lsbdistrelease: 'Trusty',
    
        }
      end
    
      describe 'standard content' do
        it { is_expected.to contain_class('apache').with('mpm_module' => 'prefork') }
      
        it {
          is_expected.to contain_apache__vhost('pe_getstarted_app').with(
            'port' => '80',
          )
        }
      
       it {
        is_expected.to contain_file('/var/www/pe_getstarted_app/index.php').with(
            'ensure'  => 'file',
            'content' => "<?php phpinfo() ?>\n",
            'mode' => '0644'
          )
        }
      end
    
      describe 'custom content' do
        let(:params) do
          { 'content' => "custom\n", }
        end
    
        it {
          is_expected.to contain_file('/var/www/pe_getstarted_app/index.php').with(
            'ensure'  => 'file',
            'content' => "custom\n",
            'mode' => '0644'
          )
        }
      end
    end
    
    ```

5.  Run `pdk validate` to check the code style and syntax of your class. 

6.  Run `pdk test unit` to run your unit test against your class.


### Adding your module class to nodes

Add the class from your example module to nodes in the console.

You can now add your new module's class to the console and apply it to nodes, following the workflow in the Adding classes getting started guide.

Congratulations! Your first module is up and running. There are plenty of additional resources about modules and the creation of modules that you can reference. Check out documentation about [Puppet Development Kit](https://docs.puppet.com/pdk/), [module fundamentals](https://docs.puppet.com/puppet/5.3/modules_fundamentals.html), [the modulepath](https://docs.puppet.com/puppet/5.3/dirs_modulepath.html), the [Beginner's guide to modules](https://docs.puppet.com/puppet/5.3/bgtm.html), and the [Puppet Forge](https://forge.puppet.com/).

**Related information**  


[Start adding classes](add_classes_gsg.md#)

