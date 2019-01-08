---
layout: default
title: "Creating Application Definitions"
canonical: "/pe/latest/app_orchestration_app_definition.html"
---

The first step in creating applications using the Puppet language is to model them in an application definition.  The application definition consists of two parts:

* The [application components](#application-components) within the application definition.

* The service resources, which are produced or consumed by application components. (These are illustrated below but are discussed in greater detail in [Producing and Consuming Service Resources](./app_orchestration_produce_consume.html) and [Writing Custom Service Resource Types](app_orchestration_writing_service_resources.html).

After you’ve created your application definition, your application can be instantiated any number of times, as discussed in [Declaring Application Instances](app_orchestration_declare_instance.html).

## Application Definitions

The **application definition** is a lot like a [defined resource type](/puppet/4.3/reference/lang_defined_types.html) except that instead of defining a chunk of reusable configuration that applies to a single node, the application definition operates at a higher level. The components you declare inside an application can be individually assigned to separate nodes you manage with Puppet.

For example, if you want to manage a simple LAMP stack, you would create an application definition that contains two components. A component for the Apache web server with your PHP application and one for the MySQL database service. You could make it fancier with multiple Apache components, a load balancer component and even a separate component for your application but our example will keep it simple.

Where class and defined resource type definitions are for modeling a collection of node configuration, the application definition is for modeling configuration at a higher-level---site or environment wide.

Within an application definition, you declare application components. These are declarations of classes or resources that express a relationship to environment-wide services. A common pattern is a three-tier application stack where a load balancer relies on one or more application servers, which rely on one or more database servers.

The application definition, therefore, describes the relationship between the application components and the exchanged service resources.

## Applications Definitions Exist in Module Manifests

Applications definitions are located in module manifests and follow [standard autoloader behavior](/puppet/4.3/reference/lang_namespaces.html#autoloader-behavior).

For example, an application directory uses the typical path: `<ENVIRONMENT DIRECTORY>/modules/<MODULE NAME>/manifests/init.pp`, where `init.pp` would define the application MODULE NAME.

Any [application component](#application-components) (classes, resources, or defined types) should be in its own file in the module’s manifests directory, and each file should have the `.pp` file extension.

[Service resources](./app_orchestration_produce_consume.html) live in a module’s `type` directory and uses the typical path  `<ENVIRONMENT DIRECTORY>/modules/<MODULE NAME>/lib/puppet/type/<SERVICE RESOURCE.rb>`.

>**Tip**: The Module Fundamentals documentation explains the [standard module layout](/puppet/4.3/reference/modules_fundamentals.html#module-layout)

## Application Components

An **application component** is an independent bit of Puppet code that can be used alongside one or more other components to create an application.  Components are commonly [defined types](/puppet/4.3/reference/lang_defined_types.html) that consist of traditional Puppet resources describing the configuration of the component (file/package/service, etc.) but can be [classes](/puppet/4.3/reference/lang_classes.html) or [native resources](/puppet/4.3/reference/lang_resources.html) too.

A type, class, or resource becomes an **application component** when you declare it in an application manifest while producing, consuming, or requiring a service resource (using the export, consume, or require metaparameters).

Here is an example of an application component definition, in this case, a mock MySQL database:

~~~
# Creates and manages a database
define lamp::db (
  $db_user,
  $db_password,
  $host = $::fqdn,
  $port = 3306,
  $database = $name,
) {
  notify { "Hello! This is the ${name}'s lamp::db component" }
{

  include mysql::bindings::php

  mysql::db { $name:
    user     => $db_user,
    password => $db_password,
  }

  ...

}
~~~

The following application component is an app server that relies on the previously defined database.

~~~
# Creates and manages an app server

define lamp::app (
  $docroot,
  $db_name,
  $db_port,
  $db_user,
  $db_host,
  $db_password,
  $host = $::fqdn,
) {
  notify { "Hello! This is the ${name}'s rgbank::web component" }

  ...

}
~~~

Finally, we have a webserver as well.

~~~
define lamp::web(
  $port = '80',
  $docroot = '/var/www/html',
  ){
  class { 'apache':
    default_mods => false,
    default_vhost => false,
  }

  apache::vhost { $name:
    port    => $port,
    docroot => $docroot,
  }
~~~


## Combining Components In An Application Definition

The application definition groups components together and defines the flow of data between them.

The application definition requires the `application` keyword and then one or more components.

In the following example, we've grouped the `lamp::db` component, the `lamp::web` component, and the `lamp::app` component in an application definition called `lamp`. Remember, the `lamp::db` component **exports** (produces) the `Sql` service resource, which is then **consumed** by the `lamp::app` component. The `lamp::web` component produces the `Http` service resource.

>**Note**: For details on service resource mapping statements, which are part of the application instance declaration, refer to [Adding Service Resource Mapping Statements](./app_orchestration_declare_instance.html#adding-service-resource-mapping-statements).

~~~
application lamp (
  $db_user,
  $db_password,
  $docroot = '/var/www/html',
) {

  lamp::web { $name:
    docroot => $docroot,
    export  => Http["lamp-${name}"],
  }

  lamp::app { $name:
    docroot => $docroot,
    consume => Sql["lamp-${name}"],
  }

  lamp::db { $name:
    db_user     => $db_user,
    db_password => $db_password,
    export      => Sql["lamp-${name}"],
  }

}
~~~

> **Notes**:
>
>- Properties specified in a component will override any values contained in a service resource.
>
>- Statements using the `export` and `consume` metaparameters to create dependencies between components [should not form cycles](/puppet/4.3/reference/lang_relationships.html#dependency-cycles).

## Validating Application Orchestration Code

When you're ready to validate Puppet code you intend to use as application orchestration code, you need to add the `--app_management` flag to the parser command. The full command to run is `puppet parser validate --app_management`.

### Service Resource Types and Service Resource Mapping Statements

For more information about service resource types, refer to [Producing and Consuming Service Resources](./app_orchestration_produce_consume.html) and [Writing Custom Service Resource Types](./app_orchestration_writing_service_resources.html).


<!-- Ethnio Activation Code -->
<script type="text/javascript" language="javascript" src="//ethn.io/77154.js" async="true" charset="utf-8"></script>