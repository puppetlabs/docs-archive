---
layout: default
title: "Managing Multi-Node Configurations with Puppet Application Orchestration"
canonical: "/pe/latest/app_orchestration_overview.html"
---

Application Orchestration provides Puppet language extensions and command-line tools to help you configure and manage multi-service and multi-node applications.

## What is Application Orchestration?

Application orchestration is:

- Extensions of the Puppet language for describing configuration relationships between components of a distributed application.
- A service that orchestrates configuration enforcement in a directed manner from the node level to the environment level.
- A command-line tool for executing orchestration jobs, inspecting those jobs, and understanding application instances declared in an environment.

## Why Use Application Orchestration?

When you use these new capabilities:

- Configuration updates are are no longer randomly applied when agents choose to check in. Instead, you can directly apply node configurations in a specified order.
- The additions to the Puppet language enable you to model how the logical components of a node configurations relate to each other.
- The Puppet Orchestrator command-line tool makes it simple to update these configurations in the appropriate order, no matter how many nodes you're configuring.

In a nutshell, when using application orchestration, you:

1. Use the Puppet language to model the desired state of your application and its supporting infrastructure, describing how information is shared between components.
2. Assign application components to Puppet nodes.
3. Use the Puppet Orchestrator to run Puppet in the correct order on the nodes that host application components.

## Language Extensions for Application Orchestration: An Overview

To understand the the usefulness of the language extensions for application orchestration, consider the following example:

A common use-case for Puppet users is the three-tier stack application infrastructure: the load-balancer, the application/web server, and the database server. When you configure these servers, you want the application server to know where the database service is and how they connect so that you can cleanly bring up the application. You then want the load balancer to automatically configure itself to balance demand on a number of application servers. When you update the configuration of these machines, or roll out a new application release, you want the three tiers to reconfigure in the correct order.

Previously in Puppet, you’d write some Puppet code for each tier of this stack, and classify the nodes responsible for each tier. Then you’d manually run Puppet on each node in the desired order, or wait for configuration to converge after several node runs. In some cases, you even used exported resources to exchange information between the application and database servers, or you pre-filled information in Hiera.

In other words---you did a lot of labor to realize your stack.

With application orchestration functionality, you model each tier as a component of a larger construct---the application---and use Puppet Enterprise to trigger the nodes’ configurations in the order you specify. When you specify the order of the configurations, information is shared between nodes (for example, services are stopped or started) to ensure each component of the stack is created in the correct order.

### The Application Definition

The [**application definition**](./app_orchestration_app_definition.html) is a lot like a defined resource type, except that instead of defining a chunk of reusable configuration that applies to a single node, the application definition operates across multiple nodes. The components you declare inside an application can be individually assigned to separate nodes that you manage with Puppet.

The application definition defines the configuration of your application infrastructure---what dependencies nodes have on each other and what information they exchange about those dependencies.

### Application Components

An [**application component**](./app_orchestration_app_definition.html##application-components) is an independent bit of Puppet code that can be used alongside one or more other components to create an application.  Components are commonly [defined types](/puppet/4.3/reference/lang_defined_types.html) that consist of traditional Puppet resources that describe the configuration of the component (for example, a file, package, or service). But components can be [classes](/puppet/4.3/reference/lang_classes.html) or [native resources](/puppet/4.3/reference/lang_resources.html) too.

A type, class, or resource becomes an **application component** when you declare it in an application manifest while producing, consuming, or requiring a **service resource **(using the `export`, `consume`, or `require` metaparameters).

### Service Resources

When you compose applications, application components can share information with each other by exporting and consuming environment-wide [**service resources**](./app_orchestration_produce_consume.html). This helps components work together even if they are on different servers. An environment service resource works like an [exported resource](/puppet/4.3/reference/lang_exported.html), providing data for other parts of the application to consume. It also helps application components express dependent relationships.

You can also [write custom service resource types](./app_orchestration_writing_service_resources.html).

## Application Orchestration is A Puppet Enterprise Feature

Application Orchestration is a Puppet Enterprise feature.

The language features work in open source Puppet, but since the Puppet Orchestrator command-line tool and service is currently available only in Puppet Enterprise, the language features don't result in ordered runs that respect node dependencies.

## Documentation Links

- [Application Orchestration: Key Concepts and Workflow](./app_orchestration_workflow.html)
   - [Creating Application Definitions](./app_orchestration_app_definition.html)
   - [Declaring Application Instances](./app_orchestration_declare_instance.html)
   - [Producing and Consuming Service Resources](./app_orchestration_produce_consume.html)
   - [Writing Custom Service Resource Types](./app_orchestration_writing_service_resources.html)
   - [Using Availability Tests](./app_orchestration_availability_tests.html)

- [Puppet Orchestrator docs](./orchestrator_intro.html)
   - [Installing the Puppet Orchestrator](./orchestrator_install.html)
   - [Running Puppet Jobs](./orchestrator_job_run.html)
   - [Viewing Puppet Jobs](./orchestrator_job_status_view.html)
   - [Reviewing Applications](./orchestrator_app.html)

