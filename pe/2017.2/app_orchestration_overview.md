---
layout: default
title: "Application orchestration: Overview"
canonical: "/pe/latest/app_orchestration_overview.html"
---

Application orchestration provides Puppet language extensions and command-line tools to help you configure and manage multi-service and multi-node applications.

## The what and why of application orchestration 

Application orchestration enables you to create and manage multi-service and multi-node applications.

Specifically, application orchestration provides:

- Extensions of the Puppet language for describing configuration relationships between components of a distributed application.
- A service that orchestrates configuration enforcement in a directed manner from the node level to the environment level.
- A command-line tool for executing orchestration jobs, inspecting those jobs, and understanding application instances declared in an environment.

When you use application orchestration capabilities:

- Configuration updates are are no longer randomly applied when agents choose to check in. Instead, you can directly apply node configurations in a specified order.
- The additions to the Puppet language enable you to model how the logical components of a node configurations relate to each other.
- The Puppet orchestrator command-line tool makes it simple to update these configurations in the appropriate order, no matter how many nodes you're configuring.

In a nutshell, when using application orchestration, you:

1. Use the Puppet language to model the desired state of your application and its supporting infrastructure, describing how information is shared between components.
2. Assign application components to Puppet nodes.
3. Use the Puppet orchestrator to run Puppet in the correct order on the nodes that host application components.

## Model a Wordpress instance

The [Puppet wordpress_app](https://forge.puppet.com/puppetlabs/wordpress_app/readme) is a module that demonstrates an example application model. The module contains application components you can use to set up a WordPress database, a PHP application server, and an HAProxy load balancer. With these components, you can build two WordPress applications: a simple LAMP stack or a complex stack that uses load-balancing.

Before beginning, please check the module's [dependencies](https://forge.puppet.com/puppetlabs/wordpress_app/dependencies) and [compatibilities](https://forge.puppet.com/puppetlabs/wordpress_app/compatibility). For r10k users, the module contains a Puppetfile that installs the needed dependencies. If you download the module with the PMT, the dependencies are installed for you. 

## Language extensions for application orchestration

One of the main features of application orchestration is its extension of the Puppet language to provide new constructs for managing multi-service and multi-node dependencies.

These language extensions include, the *application definition*, *application components*, and *service resources*.

Consider the following example:

A common use-case for Puppet users is the three-tier stack application infrastructure: the load-balancer, the application/web server, and the database server. When you configure these servers, you want the application server to know where the database service is and how they connect so that you can cleanly bring up the application. You then want the load balancer to automatically configure itself to balance demand on a number of application servers. When you update the configuration of these machines, or roll out a new application release, you want the three tiers to reconfigure in the correct order.

Previously in Puppet, you’d write some Puppet code for each tier of this stack, and classify the nodes responsible for each tier. Then you’d manually run Puppet on each node in the desired order, or wait for configuration to converge after several node runs. In some cases, you even used exported resources to exchange information between the application and database servers, or you pre-filled information in Hiera.

In other words---you did a lot of labor to realize your stack.

With application orchestration functionality, you model each tier as a component of a larger construct---the application---and use Puppet Enterprise to trigger the nodes’ configurations in the order you specify. When you specify the order of the configurations, information is shared between nodes (for example, services are stopped or started) to ensure each component of the stack is created in the correct order.

### The application definition

The [**application definition**](./app_orchestration_app_definition.html) is a lot like a defined resource type, except that instead of defining a chunk of reusable configuration that applies to a single node, the application definition operates across multiple nodes. The components you declare inside an application can be individually assigned to separate nodes that you manage with Puppet.

The application definition defines the configuration of your application infrastructure---what dependencies nodes have on each other and what information they exchange about those dependencies.

### Application components

An [**application component**](./app_orchestration_app_definition.html#application-components) is an independent bit of Puppet code that can be used alongside one or more other components to create an application.  Components are commonly [defined types]({{puppet}}/lang_defined_types.html) that consist of traditional Puppet resources that describe the configuration of the component (for example, a file, package, or service). But components can be [classes]({{puppet}}/lang_classes.html) or [native resources]({{puppet}}/lang_resources.html) too.

A type, class, or resource becomes an **application component** when you declare it in an application manifest while producing, consuming, or requiring a **service resource** (using the `export`, `consume`, or `require` metaparameters).

### Service resources

When you compose applications, application components can share information with each other by exporting and consuming environment-wide [**service resources**](./app_orchestration_produce_consume.html). This helps components work together even if they are on different servers. An environment service resource works like an [exported resource]({{puppet}}/lang_exported.html), providing data for other parts of the application to consume. It also helps application components express dependent relationships.

You can also [write custom service resource types](./app_orchestration_writing_service_resources.html).

## Application orchestration is a Puppet Enterprise feature

While the application orchestration language features work in open source Puppet, the language features alone don't result in ordered runs that respect node dependencies. The Puppet orchestrator command-line tool and service is controls ordered runs, but currently available only in Puppet Enterprise. Open source Puppet users can use the application orchestration language features, but open source Puppet does not provide any tools for controlling ordered runs.

********

**Related links**

- [Application orchestration: Key concepts and workflow](./app_orchestration_workflow.html)
   - [Creating application definitions](./app_orchestration_app_definition.html)
   - [Declaring application instances](./app_orchestration_declare_instance.html)
   - [Producing and consuming service resources](./app_orchestration_produce_consume.html)
   - [Writing custom service resource types](./app_orchestration_writing_service_resources.html)
   - [Using availability tests](./app_orchestration_availability_tests.html)

- [Puppet orchestrator docs](./orchestrator_intro.html)
   - [Installing the Puppet orchestrator](./orchestrator_install.html)
   - [Running Puppet jobs](./orchestrator_job_run.html)
   - [Viewing Puppet jobs](./orchestrator_job_status_view.html)
   - [Reviewing applications](./orchestrator_app.html)

