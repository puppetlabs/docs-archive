---
layout: default
title: "Preparing for migration with the catalog_preview module"
canonical: "/pe/latest/migrate_pe_catalog_preview.html"
description: "Preparing for PE 2016.2 with the catalog_preview module."
---

[catalogpreview]: https://forge.puppet.com/puppetlabs/catalog_preview
[license]: ./install_what_and_where.html#license-file
[parser]: /puppet/3.8/reference/config_file_environment.html#parser
[roles]: ./r_n_p_intro.html

The catalog_preview module helps you prepare for migration or upgrade from Puppet 3.8.x to Puppet Enterprise 2016.2. 

This module compiles and compares two catalogs: one in a baseline environment using the Puppet 3 language (or "current") parser, and another in a preview environment using the "future" parser to validate your syntax against the new Puppet 4 language. The output from this comparison highlights issues you might experience when you migrate from Puppet Enterprise 3.8.x to Puppet Enterprise 2016.2.

In this workflow, you'll point your preview environment at a branch of the environment you want to migrate, and then configure the preview environment to use the Puppet 4 ("future") parser. This way, backwards-incompatible changes can be made in the preview environment without affecting production. You can then use the diff, the catalog, and the log outputs provided by catalog_preview to make changes to the preview environment until you feel it is ready to move into production.

For full details about the catalog_preview module, see the module [README][catalogpreview].

## Setup

### Prerequisites

* Puppet Enterprise 3.8.6
* A valid Puppet Enterprise [license][license]

### Install catalog_preview

Install the [catalog_preview][catalogpreview] module:

`puppet module install puppetlabs/catalog_preview`

After the module is installed, you're ready to begin.

## Compare your environment

If you have multiple environments for testing, we recommend starting with your most unstable environment to start making changes. You'll push changes to this environment that will eventually move to your production environment. 

Let's say you have three Puppet environments: production, staging, and development. Your development environment is the least stable, and you test out a lot of different changes there. When you're happy with those changes, you promote them to staging and eventually, to production. In this example, we'll walk through testing that development environment.

1. First make a copy of the current development environment:


	    cd /etc/puppetlabs/puppet/environments
	    cp -r development development_future_parser_testing


    Now you have an environment---development_future_parser_testing---in which to make changes and perform testing. 

2. Next, enable the future parser for this environment. To do this, edit the [environment.conf][parser] and add the line `parser = future` to the file. (If your environment doesn't yet have an environment.conf, you'll need to first create it.)

        To check the setting, run `puppet master --configprint parser --environment development_future_parser_testing`. It should return the value 'future'.

      > Note that the agent you are testing must have checked in with the master at least one time before. If this is a new agent, you can run `puppet agent -t` on the agent to have it check in with the master before you use the preview tool.

3. Now you're ready to start comparing the environments with `puppet preview`. 


	    puppet preview master381-centos --preview_environment development_future_parser_testing --baseline_environment development --migrate 3.8/4.0 --view overview

In this example, we've passed in `master381-centos`. For your own testing, you'll want to choose a representative node for [each role][roles] you have. So if you have three major roles---webserver, database, and application_server---then you'll test a node from each of those roles. Later, you will be able to run the tool on all nodes, once you think you've determined 90 percent of your migration issues. 

We also passed `--preview_environment development_future_parser_testing --baseline_environment development`, which performs the following tasks: 

* Compiles a catalog in the preview environment using future parser (in this case, development_future_parser_testing).
* Compiles a catalog in the baseline environment using current parser (in this case, development).
* Compares the two catalogs to find differences.

Note that you must specify the baseline environment to compare your preview to any environment other than production.

## Reading the output

On a brand new installation of PE 3.8.x, the output for our example looks like this:

~~~
[root@master381-centos files]# puppet preview master381-centos --preview_environment development_future_parser_testing --baseline_environment development --migrate 3.8/4.0 --view overview
Stats
 Total number of nodes:  1, 100.0%
  Conflicting..........: 1, 100.0%
  Compliant............: 0,   0.0%
  Equal................: 0,   0.0%

Preview Warnings (by issue)
  MIGRATE4_EQUALITY_TYPE_MISMATCH (1)
    /opt/puppet/share/puppet/modules/puppet_enterprise/manifests/params.pp:158:26
  MIGRATE4_REVIEW_IN_EXPRESSION (1)
    /opt/puppet/share/puppet/modules/pe_concat/manifests/fragment.pp:93:20

Changes per resource type

Top ten nodes with most issues
  master381-centos (1)
~~~

There are four sections to this output:

1. Stats
2. Preview Warnings (by issue)
3. Changes per Resource Type
4. Top ten nodes with most issues

### Stats

This section tells you how many of the nodes are conflicting, compliant, or equal.

The catalog is considered to be **compliant** if it has no removals or conflicts compared to the baseline catalog; it may contain additions, however. The catalog is **equal** if it contains the same set of resources, the same set of dependencies, and all attributes have equal values. This information becomes more useful when you run the comparison against many nodes.  

### Preview warnings (by issue)

This section displays warnings along with a file location and a line number that you can investigate for more information about the issues. The two warnings in the example above always show up when you run the tool on PE 3.8.x. 

In addition to these warnings, you might get other warnings. Please see the catalog_preview README to learn more about specific [warnings](https://forge.puppetlabs.com/puppetlabs/catalog_preview#migration-warnings).

#### Warning details

~~~
MIGRATE4_EQUALITY_TYPE_MISMATCH (1)
    /opt/puppet/share/puppet/modules/puppet_enterprise/manifests/params.pp:158:26
~~~

The above warning indicates that there's a mismatch between two different types---in this case, a number and a string. Warnings of this type should always be corrected as they are likely to change behavior between versions.

Note that a bug in PE can cause this warning if you turn on future parser and you have agents running a PE version older than 3.7. In that case, the agents would get an incorrect configuration of MCollective. You can work around this issue by removing the `profile::mcollective::agent` class from all your nodes. This issue should be patched in a future release of Puppet Enterprise.

~~~
MIGRATE4_REVIEW_IN_EXPRESSION (1)
    /opt/puppet/share/puppet/modules/pe_concat/manifests/fragment.pp:93:20
~~~

This warning indicates that you've used an 'in' expression. These warnings do not always need to be corrected. In this case, if you look at the code below, the in expression is correctly used. We recommend that you first resolve issues in the changed resources section before trying to resolve this warning, as your changed resources section might reveal why you got this warning.

~~~
if ! ($my_ensure in ['', 'present', 'absent', 'file' ]) {
    $ensure_target = $my_ensure
  } else {
    $ensure_target = undef
  }
~~~

### Changes per resource type

This section tells you if something about a resource is different between the preview catalog and the baseline catalog. This section is also the reason you want to use the exact same code to preview; otherwise, you'll see a lot of items that aren't related to changes in the parser.

### Top 10 nodes with most issues

Once again, this section becomes useful when you are previewing multiple nodes.

## Further help

In addition to this workflow and the [module README][catalogpreview], you can get catalog_preview help on the command line with:

`puppet preview --help`

You can also get help for the catalog-delta with:

`puppet preview --schema help`
