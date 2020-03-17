# Puppet platform documentation for PE

Puppet Enterprise \(PE\) is built on the Puppet platform which has several components: Puppet, Puppet Server, Facter, Hiera, and PuppetDB. This page describes each of these platform components, and links to the component docs.

## Puppet

-   [Puppet docs](https://puppet.com/docs/puppet/6.10/)


Puppet is the core of our configuration management platform. It consists of a programming language for describing desired system states, an agent that can enforce desired states, and several other tools and services.

Right now, you’re reading the PE manual; the Puppet reference manual is a separate section of our docs site. After you’ve followed a link there, you can use the navigation sidebar to browse other sections of the manual.

**Note:** The Puppet manual has information about installing the open source release of Puppet. As a PE user, ignore those pages.

The following pages are good starting points for getting familiar with Puppet:

-   **Language**

    -   [An outline of how the Puppet language works.](https://puppet.com/docs/puppet/6.10/lang_summary.html)
        -   [Resources](https://puppet.com/docs/puppet/6.10/lang_resources.html),  [variables](https://puppet.com/docs/puppet/6.10/lang_variables.html),  [conditional statements](https://puppet.com/docs/puppet/6.10/lang_conditional.html), and  [relationships and ordering](https://puppet.com/docs/puppet/6.10/lang_relationships.html) are the fundamental pieces of the Puppet language.
    -   [Classes](https://puppet.com/docs/puppet/6.10/lang_classes.html) and  [defined types](https://puppet.com/docs/puppet/6.10/lang_defined_types.html) are how you organize Puppet code into useful chunks. Classes are the main unit of Puppet code you’ll be interacting with on a daily basis. You can assign classes to nodes in the PE console.
    -   [Facts and built-in variables](https://puppet.com/docs/puppet/6.10/lang_facts_and_builtin_vars.html) explains the special variables you can use in your Puppet manifests.
-   **Modules**

    -   Most Puppet code goes in modules. We explain how modules work  [here.](https://puppet.com/docs/puppet/6.10/modules_fundamentals.html)
    -   There are also guides to  [installing modules](https://puppet.com/docs/puppet/6.10/modules_installing.html) and  [publishing modules](https://puppet.com/docs/puppet/6.10/modules_publishing.html) on the Forge.
    -   Use the code management features included in PE to control your modules instead of installing by hand. See Managing and deploying Puppet code \(in the PE manual\) for more details.
-   **Services and commands**

    -   [An overview of Puppet’s architecture.](https://puppet.com/docs/puppet/6.10/architecture.html)
    -   [A list of the main services and commands you’ll interact with.](https://puppet.com/docs/puppet/6.10/services_commands.html)
    -   [Notes on running Puppet’s commands on Windows.](https://puppet.com/docs/puppet/6.10/services_commands_windows.html)
-   **Built-in resource types and functions**

    -   [The resource type reference](https://puppet.com/docs/puppet/6.10/type.html) has info about all of the built-in Puppet resource types.
    -   [The function reference](https://puppet.com/docs/puppet/6.10/function.html) does the same for the built-in functions.
-   **Important directories and files**

    -   Most of your Puppet content goes in environments. Find out more about environments  [here.](https://puppet.com/docs/puppet/6.10/environments.html)
    -   The  [codedir](https://puppet.com/docs/puppet/6.10/dirs_codedir.html) contains code and data and the  [confdir](https://puppet.com/docs/puppet/6.10/dirs_confdir.html) contains config files. The  [modulepath](https://puppet.com/docs/puppet/6.10/dirs_modulepath.html) and the  [main manifest](https://puppet.com/docs/puppet/6.10/dirs_manifest.html) both depend on the current environment.
-   **Configuration**

    -   The main config file for Puppet is `/etc/puppetlabs/puppet/puppet.conf`. Learn more about  [Puppet’s settings](https://puppet.com/docs/puppet/6.10/config_about_settings.html), and  [about `puppet.conf`](https://puppet.com/docs/puppet/6.10/config_file_main.html) itself.
    -   There are also a bunch of other config files used for special purposes. Go to the  [page about `puppet.conf`](https://puppet.com/docs/puppet/6.10/config_file_main.html) and check the navigation sidebar for a full list.

## Puppet Server

-   [Puppet Server docs](https://puppet.com/docs/puppetserver/latest/)

Puppet Server is the JVM application that provides the core Puppet HTTPS services. Whenever Puppet agent checks in to request a configuration catalog for a node, it contacts Puppet Server.

For the most part, PE users don’t need to directly manage Puppet Server, and the Puppet reference manual \(above\) has all the important info about how Puppet Server evaluates the Puppet language and loads environments and modules. However, some users might need to access the  [environment cache](https://puppet.com/docs/puppetserver/latest/admin-api/v1/environment-cache.html) and  [JRuby pool](https://puppet.com/docs/puppetserver/latest/admin-api/v1/jruby-pool.html) administrative APIs, and there’s lots of interesting background information in the rest of the Puppet Server docs.

**Note:** The Puppet Server manual has information about installing the open source release of Puppet Server. As a PE user, ignore those pages. Additionally, the Puppet Server config files in PE are managed with a built-in Puppet module; to change most settings, set the appropriate class parameters in the console.

## Facter

-   [Facter docs](https://puppet.com/docs/facter/3.12/)

Facter is a system profiling tool. Puppet agent uses it to send important system info to Puppet Server, which can access that info when compiling that node’s catalog.

-   For a list of variables you can use in your code, check out the  [core facts reference](https://puppet.com/docs/facter/3.12/core_facts.html).
-   You can also write your own custom facts. See the  [custom fact overview](https://puppet.com/docs/facter/3.12/fact_overview.html) and the  [custom fact walkthrough](https://puppet.com/docs/facter/3.12/custom_facts.html).

## Hiera

-   [Hiera docs](https://puppet.com/docs/puppet/6.10/hiera_intro.html)

Hiera is a hierarchical data lookup tool. You can use it to configure your Puppet classes.

Start with the  [overview](https://puppet.com/docs/puppet/6.10/hiera_intro.html) and use the navigation sidebar to get around.

**Note:** Hiera 5 is a backwards-compatible evolution of Hiera, which is built into Puppet. To provide some backwards-compatible features, it uses the classic Hiera 3 codebase. This means “Hiera” is still version 3.x, even though this Puppet Enterprise version uses Hiera 5.

## PuppetDB

-   [PuppetDB docs](https://puppet.com/docs/puppetdb/latest/)

PuppetDB collects the data Puppet generates, and offers a powerful query API for analyzing that data. It’s the foundation of the PE console, and you can also use the API to build your own applications.

If you’re interacting with PuppetDB directly, you’ll mostly be using the query API.

-   [The query tutorial page](https://puppet.com/docs/puppetdb/latest/api/query/tutorial.html) walks you through the process of building and executing a query.
-   [The query structure page](https://puppet.com/docs/puppetdb/latest/api/query/v4/query.html) explains the fundamentals of using the query API.
-   [The cURL tips page](https://puppet.com/docs/puppetdb/latest/api/query/curl.html) has useful information about testing the API from the command line.
-   You can use the navigation sidebar to browse the rest of the query API docs.

**Note:** The PuppetDB manual has information about installing the open source release of PuppetDB. As a PE user, ignore those pages.

**Related information**  


[Managing and deploying Puppet code](managing_puppet_code.md)

