---
layout: default
title: "An overview of PE's platform documentation"
canonical: "/pe/latest/puppet_overview.html"
---

{% capture puppetversion %}{{ page.doc.my_versions.puppet }}{% endcapture %}
{% capture hieraversion  %}{{ page.doc.my_versions.hiera }}{% endcapture %}
{% capture facterversion %}{{ page.doc.my_versions.facter }}{% endcapture %}
{% capture serverversion %}{{ page.doc.my_versions.puppetserver }}{% endcapture %}
{% capture dbversion     %}{{ page.doc.my_versions.puppetdb }}{% endcapture %}

Puppet Enterprise is built on the Puppet configuration management platform. This platform has several components, and the most important ones are:

- [Puppet](#puppet)
- [Puppet server](#puppet-server)
- [Facter](#facter)
- [Hiera](#hiera)
- [PuppetDB](#puppetdb)

This page describes the components of our platform, and links to the correct component docs for this PE version.

## Puppet

* [The Puppet {{puppetversion}} reference manual]({{puppet}}/)

Puppet is the core of our configuration management platform. It consists of a special programming language for describing desired system states, an agent that can enforce desired states, and several other tools and services.

Right now, you're reading the PE manual; the Puppet {{puppetversion}} reference manual is a separate section of our docs site. Once you've followed a link there, you can use the navigation sidebar to browse other sections of the manual.

> **Note:** The Puppet manual has information about installing the open source release of Puppet. As a PE user, you should ignore those pages.

The following pages are good starting points for getting familiar with Puppet:

### Language

* [An outline of how the Puppet language works.]({{puppet}}/lang_summary.html)
* [Resources]({{puppet}}/lang_resources.html), [variables]({{puppet}}/lang_variables.html), [conditional statements]({{puppet}}/lang_conditional.html), and [relationships and ordering]({{puppet}}/lang_relationships.html) are the fundamental pieces of the Puppet language.
* [Classes]({{puppet}}/lang_classes.html) and [defined types]({{puppet}}/lang_defined_types.html) are how you organize Puppet code into useful chunks.
    * Classes are the main unit of Puppet code you'll be interacting with on a daily basis! You can assign classes to nodes in the PE console.
* [Facts and built-in variables]({{puppet}}/lang_facts_and_builtin_vars.html) explains the special variables you can use in your Puppet manifests.

### Modules

* Most Puppet code should go in modules. We explain how modules work [here.]({{puppet}}/modules_fundamentals.html)
* There are also guides to [installing modules]({{puppet}}/modules_installing.html) and [publishing modules]({{puppet}}/modules_publishing.html) on the Puppet Forge.
* As a PE user, we recommend that you use PE's code management features to control your modules instead of installing by hand. See [Managing and deploying Puppet code](./cmgmt_managing_code.html) (in the PE manual) for more details.

### Services and commands

* [An overview of Puppet's architecture.]({{puppet}}/architecture.html)
* [A list of the main services and commands you'll interact with.]({{puppet}}/services_commands.html)
* [Notes on running Puppet's commands on Windows.]({{puppet}}/services_commands_windows.html)

### Built-in resource types and functions

* [The resource type reference]({{puppet}}/type.html) has info about all of Puppet's built-in resource types.
* [The function reference]({{puppet}}/function.html) does the same for the built-in functions.

### Important directories and files

* Most of your Puppet content goes in environments. Find out more about environments [here.]({{puppet}}/environments.html)
* The [codedir]({{puppet}}/dirs_codedir.html) contains code and data and the [confdir]({{puppet}}/dirs_confdir.html) contains config files. The [modulepath]({{puppet}}/dirs_modulepath.html) and the [main manifest]({{puppet}}/dirs_manifest.html) both depend on the current environment.

### Configuration

* Puppet's main config file is `/etc/puppetlabs/puppet/puppet.conf`. Learn more about [Puppet's settings]({{puppet}}/config_about_settings.html), and [about puppet.conf]({{puppet}}/config_file_main.html) itself.
* There are also a bunch of other config files used for special purposes. Go to the [page about puppet.conf]({{puppet}}/config_file_main.html) and check the navigation sidebar for a full list.

## Puppet server

* [Puppet server {{serverversion}} docs]({{puppetserver}}/)

Puppet Server is the JVM application that provides Puppet's core HTTPS services. Whenever Puppet agent checks in to request a configuration catalog for a node, it contacts Puppet Server.

For the most part, PE users don't need to directly manage Puppet Server, and the Puppet reference manual (above) has all the important info about how Puppet Server evaluates the Puppet language and loads environments and modules. However, some users might need to access the [environment cache]({{puppetserver}}/admin-api/v1/environment-cache.html) and [JRuby pool]({{puppetserver}}/admin-api/v1/jruby-pool.html) administrative APIs, and there's lots of interesting background information in the rest of the Puppet Server docs.

> **Note:** The Puppet Server manual has information about installing the open source release of Puppet Server. As a PE user, you should ignore those pages. Additionally, Puppet Server's config files in PE are managed with a built-in Puppet module; to change most settings, you should set the appropriate class parameters in the PE console.

## Facter

* [Facter {{facterversion}} docs]({{facter}}/)

Facter is a system profiling tool. Puppet agent uses it to send important system info to Puppet Server, which can access that info when compiling that node's catalog.

* For a list of variables you can use in your code, check out the [core facts reference]({{facter}}/core_facts.html).
* You can also write your own custom facts. See the [custom fact overview]({{facter}}/fact_overview.html) and the [custom fact walkthrough]({{facter}}/custom_facts.html).

## Hiera

* [Hiera {{hieraversion}} docs]({{hiera}}/)

Hiera is a hierarchical data lookup tool. You can use it to configure your Puppet classes.

Start with the [overview]({{hiera}}/index.html) and use the navigation sidebar to get around.


## PuppetDB

* [PuppetDB {{dbversion}} docs]({{puppetdb}}/)

PuppetDB collects the data Puppet generates, and offers a powerful query API for analyzing that data. It's the foundation of the PE console, and you can also use the API to build your own applications.

If you're interacting with PuppetDB directly, you'll mostly be using the query API.

* [The query tutorial page]({{puppetdb}}/api/query/tutorial.html) walks you through the process of building and executing a query.
* [The query structure page]({{puppetdb}}/api/query/v4/query.html) explains the fundamentals of using the query API.
* [The cURL tips page]({{puppetdb}}/api/query/curl.html) has useful information about testing the API from the command line.
* You can use the navigation sidebar to browse the rest of the query API docs.

> **Note:** The PuppetDB manual has information about installing the open source release of PuppetDB. As a PE user, you should ignore those pages.
