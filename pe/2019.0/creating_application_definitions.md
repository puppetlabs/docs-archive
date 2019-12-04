# Creating application definitions

When you create an applications using the Puppet language, you model it in an application definition. An application definition consists of two parts: application components and service resources.

After you’ve created your application definition, your application can be instantiated any number of times, as discussed in declaring application instances.

## Application definitions

The application definition is a lot like a defined resource type except that instead of defining a chunk of reusable configuration that applies to a single node, the application definition operates at a higher level. The components you declare inside an application can be individually assigned to separate nodes you manage with Puppet.

For example, if you want to manage a simple LAMP stack, you would create an application definition that contains two components. A component for the Apache web server with your PHP application and one for the MySQL database service. You could make it fancier with multiple Apache components, a load balancer component and even a separate component for your application, but our example keeps it simple.

Where class and defined resource type definitions are for modeling a collection of node configuration, the application definition is for modeling configuration at a higher-level—site or environment wide.

Within an application definition, you declare application components. These are declarations of classes or resources that express a relationship to environment-wide services. A common pattern is a three-tier application stack where a load balancer relies on one or more application servers, which rely on one or more database servers.

The application definition, therefore, describes the relationship between the application components and the exchanged service resources.

Applications definitions exist in module manifests:

-   Applications definitions are located in module manifests and follow [standard autoloader behavior](https://docs.puppet.com/puppet/5.3/lang_namespaces.html#autoloader-behavior). For example, an application directory uses the typical path: `<ENVIRONMENT DIRECTORY>/modules/<MODULE NAME>/manifests/init.pp`, where `init.pp` would define the application MODULE NAME.

-   Any application component \(classes, resources, or defined types\) should be in its own file in the module’s manifests directory, and each file should have the `.pp` file extension.

-   Service resources live in a module’s `type` directory and uses the typical path `<ENVIRONMENT DIRECTORY>/modules/<MODULE NAME>/lib/puppet/type/<SERVICE RESOURCE.rb>`.


**Tip:** The [module fundamentals documentation](https://docs.puppet.com/puppet/5.3/modules_fundamentals.html#module-layout) explains the standard module layout.

### Application components in application definitions

An *application component* is an independent bit of Puppet code that can be used alongside one or more other components to create an application. Components are commonly [defined types](https://docs.puppet.com/puppet/5.3/lang_defined_types.html) that consist of traditional Puppet resources describing the configuration of the component \(its files, packages, and services\) but can be [classes](https://docs.puppet.com/puppet/5.3/lang_classes.html) or [native resources](https://docs.puppet.com/puppet/5.3/lang_resources.html) too.

A type, class, or resource becomes an application component when you declare it in an application manifest while producing, consuming, or requiring a service resource \(using the `export`, `consume`, or `require` statements\).

Here is an example of an application component definition, in this case, a mock MySQL database:

```
# Creates and manages a database
define lamp::db (
  $db_user,
  $db_password,
  $host = $::fqdn,
  $port = 3306,
  $database = $name,
) {
  notify { "Hello! This is the ${name}'s lamp::db component" }

  include mysql::bindings::php

  mysql::db { $name:
    user     => $db_user,
    password => $db_password,
  }

  ...

}

Lamp::Db produces Sql {
  host => $host,
  port => $port,
  database => $database,
  user => $db_user,
  password => $db_password,
}

```

The following application component is an app server that relies on the previously defined database.

```
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

Lamp::App consumes Sql {
  db_host => $host,
  db_port => $port,
  db_name => $database,
  db_user => $user,
  db_password => $password,
}

```

Finally, we have a webserver as well.

```
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
}
```

### Combining application components in an application definition

The *application definition* groups components together and defines the flow of data between them. Most applications rely on some combination of components.

The application definition requires the `application` keyword and then one or more components.

In the following example, we've grouped the `lamp::db` component, the `lamp::web` component, and the `lamp::app` component in an application definition called `lamp`. Remember, the `lamp::db` component **exports** \(produces\) the `Sql` service resource, which is then **consumed** by the `lamp::app` component. The `lamp::web` component produces the `Http` service resource.

**Note:** For details on service resource mapping statements, which are part of the application instance declaration, refer to Adding service resource mapping statements. For more information about service resource types, refer to Producing and consuming service resources and Writing custom service resource types.

```
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

```

**Related information**  


[Producing and consuming service resources](producing_and_consuming_service_resources.md#)

[Writing custom service resource types](writing_custom_service_resource_types.md)

[Adding service resource mapping statements](declaring_application_instances.md#)

