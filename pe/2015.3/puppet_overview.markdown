---
layout: default
title: "An Overview of PE's Platform Documentation"
canonical: "/pe/latest/puppet_overview.html"
---

{% capture puppetversion %}4.3{% endcapture %}
{% capture hieraversion  %}3.0{% endcapture %}
{% capture facterversion %}3.1{% endcapture %}
{% capture serverversion %}2.2{% endcapture %}
{% capture dbversion     %}3.2{% endcapture %}

Puppet Enterprise is built on the Puppet configuration management platform. This platform has several components, and the most important ones are:

- [Puppet](#puppet)
- [Puppet Server](#puppet-server)
- [Facter](#facter)
- [Hiera](#hiera)
- [PuppetDB](#puppetdb)

This page describes the components of our platform, and links to the correct component docs for this PE version.

## Puppet

* [The Puppet {{puppetversion}} Reference Manual](/puppet/{{puppetversion}}/reference/)

Puppet is the core of our configuration management platform. It consists of a special programming language for describing desired system states, an agent that can enforce desired states, and several other tools and services.

Right now, you're reading the PE manual; the Puppet {{puppetversion}} reference manual is a separate section of our docs site. Once you've followed a link there, you can use the navigation sidebar to browse other sections of the manual.

> **Note:** The Puppet manual has information about installing the open source release of Puppet. As a PE user, you should ignore those pages.

The following pages are good starting points for getting familiar with Puppet:

### Language

* [An outline of how the Puppet language works.](/puppet/{{puppetversion}}/reference/lang_summary.html)
* [Resources](/puppet/{{puppetversion}}/reference/lang_resources.html), [Variables](/puppet/{{puppetversion}}/reference/lang_variables.html) [Conditional Statements](/puppet/{{puppetversion}}/reference/lang_conditional.html), and [Relationships and Ordering](/puppet/{{puppetversion}}/reference/lang_relationships.html) are the fundamental pieces of the Puppet language.
* [Classes](/puppet/{{puppetversion}}/reference/lang_classes.html) and [Defined Types](/puppet/{{puppetversion}}/reference/lang_defined_types.html) are how you organize Puppet code into useful chunks.
    * Classes are the main unit of Puppet code you'll be interacting with on a daily basis! You can assign classes to nodes in the PE console.
* [Facts and Built-in Variables](/puppet/{{puppetversion}}/reference/lang_facts_and_builtin_vars.html) explains the special variables you can use in your Puppet manifests.

### Modules

* Most Puppet code should go in modules. [We explain how modules work here.](/puppet/{{puppetversion}}/reference/modules_fundamentals.html)
* There are also guides to [installing modules](/puppet/{{puppetversion}}/reference/modules_installing.html) and [publishing modules on the Puppet Forge.](/puppet/{{puppetversion}}/reference/modules_publishing.html)
* As a PE user, we recommend that you use PE's code management features to control your modules instead of installing by hand. See [Managing and Deploying Puppet Code](./cmgmt_managing_code.html) (in the PE manual) for more details.

### Services and Commands

* [An overview of Puppet's architecture.](/puppet/{{puppetversion}}/reference/architecture.html)
* [A list of the main services and commands you'll interact with.](/puppet/{{puppetversion}}/reference/services_commands.html)
* [Notes on running Puppet's commands on Windows.](/puppet/{{puppetversion}}/reference/services_commands_windows.html)

### Built-in Resource Types and Functions

* [The resource type reference](/references/{{puppetversion}}.latest/type.html) has info about all of Puppet's built-in resource types.
* [The function reference](/references/{{puppetversion}}.latest/function.html) does the same for the built-in functions.

### Important Directories and Files

* Most of your Puppet content goes in environments. [Find out more about environments here.](/puppet/{{puppetversion}}/reference/environments.html)
* The [codedir](/puppet/{{puppetversion}}/reference/dirs_codedir.html) contains code and data] and the [confdir](/puppet/{{puppetversion}}/reference/dirs_confdir.html) contains config files. The [modulepath](/puppet/{{puppetversion}}/reference/dirs_modulepath.html) and the [main manifest](/puppet/{{puppetversion}}/reference/dirs_manifest.html) both depend on the current environment.

### Configuration

* Puppet's main config file is `/etc/puppetlabs/puppet/puppet.conf`. [Learn more about Puppet's settings](/puppet/{{puppetversion}}/reference/config_about_settings.html), and [about puppet.conf itself](/puppet/{{puppetversion}}/reference/config_file_main.html).
* There are also a bunch of other config files used for special purposes. Go to the [page about puppet.conf](/puppet/{{puppetversion}}/reference/config_file_main.html) and check the navigation sidebar for a full list.

## Puppet Server

* [Puppet Server {{serverversion}} Docs](/puppetserver/{{serverversion}}/)

Puppet Server is the JVM application that provides Puppet's core HTTPS services. Whenever Puppet agent checks in to request a configuration catalog for a node, it contacts Puppet Server.

For the most part, PE users don't need to directly manage Puppet Server, and the Puppet reference manual (above) has all the important info about how Puppet Server evaluates the Puppet language and loads environments and modules. However, some users might need to access the [environment cache](/puppetserver/{{serverversion}}/environment-cache.html) and [JRuby pool](/puppetserver/{{serverversion}}/jruby-pool.html) administrative APIs, and there's lots of interesting background information in the rest of the Puppet Server docs.

> **Note:** The Puppet Server manual has information about installing the open source release of Puppet Server. As a PE user, you should ignore those pages. Additionally, Puppet Server's config files in PE are managed with a built-in Puppet module; to change most settings, you should set the appropriate class parameters in the PE console.


## Facter

* [Facter {{facterversion}} Docs](/facter/{{facterversion}}/)

Facter is a system profiling tool. Puppet agent uses it to send important system info to Puppet Server, which can access that info when compiling that node's catalog.

* For a list of variables you can use in your code, check out the [core facts reference](/facter/{{facterversion}}/core_facts.html).
* You can also write your own custom facts. See the [custom fact overview](/facter/{{facterversion}}/fact_overview.html) and the [custom fact walkthrough](/facter/{{facterversion}}/custom_facts.html).

## Hiera

* [Hiera {{hieraversion}} Docs](/hiera/{{hieraversion}}/)

Hiera is a hierarchical data lookup tool. You can use it to configure your Puppet classes.

Start with the [overview](/hiera/{{hieraversion}}/index.html) and use the navigation sidebar to get around.


## PuppetDB

* [PuppetDB {{dbversion}} Docs](/puppetdb/{{dbversion}}/)

PuppetDB collects the data Puppet generates, and offers a powerful query API for analyzing that data. It's the foundation of the PE console, and you can also use the API to build your own applications.

If you're interacting with PuppetDB directly, you'll mostly be using the query API.

* [The Query Tutorial page](/puppetdb/{{dbversion}}/api/query/tutorial.html) walks you through the process of building and executing a query.
* [The Query Structure page](/puppetdb/{{dbversion}}/api/query/v4/query.html) explains the fundamentals of using the query API.
* [The Curl Tips page](/puppetdb/{{dbversion}}/api/query/curl.html) has useful information about testing the API from the command line.
* You can use the navigation sidebar to browse the rest of the query API docs.

> **Note:** The PuppetDB manual has information about installing the open source release of PuppetDB. As a PE user, you should ignore those pages.
