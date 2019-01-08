---
layout: default
title: "Producing and Consuming Service Resources"
canonical: "/pe/latest/app_orchestration_produce_consume.html"
---

When you compose applications, application components can share information with each other by exporting and consuming environment-wide **service resources**. This helps components work together even if they are managed by Puppet on different servers. An environment service resource works like an [exported resource](/puppet/4.3/reference/lang_exported.html), providing data for other parts of the application to consume but also helps application components express inter-dependent relationships.

Specifically service resources:

* Transport data between nodes participating in an application.

* Provide an abstraction so that you no longer need to hard-code dependencies in modules.

* Provide service resource [availability tests](./latest/app_orchestration_availability_tests.html) at the node level to understand when a component is “ready.” Service readiness can tell the Puppet agent to pause execution until the required service is available.

* Live in a module’s `type` directory and uses the typical path  `<ENVIRONMENT DIRECTORY>/modules/<MODULE NAME>/lib/puppet/type/<SERVICE RESOURCE.rb>`.

Essentially, service resources serve as checkpoints that the Puppet master uses to determine when it is safe to trigger a Puppet run on a node that consumes a resource.

Service resource instances must be [unique across an environment](/puppet/4.3/reference/lang_exported.html#uniqueness). Like other kinds of resource uniqueness, this means they must have a unique type and title. Also, just like exported resources, service resources can be located using [resource collectors](puppet/latest/reference/lang_collectors.html).

Service resources are stored, like other resources, in PuppetDB. They are subject to the lifecycle of any resource and the node whose catalog they are in. In addition, service resources adhere to the [standard PuppetDB rules for purging nodes and resources](/puppetdb/3.2/maintain_and_tune.html#deactivate-or-expire-decommissioned-nodes).

# Exporting and Consuming Service Resources

## Producing a Service Resource With `export`

When declaring an application component, the `export` metaparam is used to produce an instance of the service resource as produced by the node managing that application component.

In the following example, the `myssql::db` application component exports the `Sql` service resource. (The values of the parameters for the exported resource are defined in the component.)

~~~
  mysql::db { $name: 
    user      => $wp_db_user, 
    password  => $wp_db_password,
    export    => Sql[$name],
  }
~~~

### Syntax

`'export' '=>' CapabilityType[Name]`

The general form of the `export` statement is structured like an attribute/value pair:

* The `export` keyword.
* A `=>` (called an arrow, “fat comma,” or “hash rocket”).
* The name of the capability type, followed by a pair of brackets (`[ ]`) that contain the name to be given the service resource.

The `CapabilityType` must be the name of a service type for which there is a `produces` declaration for the type of resource being instantiated.

The resulting service resource will have the name that is specified in the `export` reference; the remaining attributes of the service resource will be filled by evaluating the corresponding `produces` declaration.

The produced service resource must be unique, by type and title, within the current environment.

## Consuming A Service Resource With `consume`

When instantiating a resource that is declared to consume a capability, the `consume` statement can be used to fill attributes of the resource being instantiated.

In the following example, the values of the wordpress::instance::app are assigned when the component consumes the `Sql` service resource.

~~~
wordpress::instance::app { $name:
      install_dir => "/var/www/${name}",
       consume     => Sql[$name],
  }
~~~

### Syntax

`'consume''=>' CapabilityType[Name]`

The general form of the `consume` statement is structured like an attribute/value pair:

* The `consume` keyword.
* A `=>` (called an arrow, “fat comma,” or “hash rocket”).
* The name of the capability type, followed by a pair of brackets (`[ ]`) that contain the name to be given the service resource.

The reference `CapabilityType[Name]` must specify a service resource that is produced on a node, including the current node, in the current environment.

### `require` A Service Resource

Requiring a resource declares a dependency between a component and a service resource.

If you need to depend on a service resource but don't need to consume its information, you can use the [`require` metaparameter](/references/4.3.latest/metaparameter.html#require), which behaves as it does elsewhere in Puppet.

# Mapping Information from Components to Service Resources

## The `produces` statement

The `produces` statement allows you to define a mapping from a component providing a service to a service resource. Further, it allows you to define how a service resource is created when an application is configured, and it defines the complete content of the service resource.

When you produce a service resource, you have access to all the top-level variables and facts available on the source node, as well as the properties of the source component. As noted below, function calls are evaluated only during Puppet runs that produce the source node's catalog.

In this example, the `mysql::db` defined type **produces** the `Sql` resource capability.

~~~
Mysql::Db produces Sql {
  user      => $user,
  password  => $password,
  host      => pick($::mysql_host_override, $::fqdn),
  #port     => not used here, will default as described in the definition
  database  => $dbname,
  type      => 'mysql',
}
~~~

Let's examine some of the use-cases you see in this statement:

* To capture information about the component, its properties can be accessed directly. In this example `user`, `password`, and `database` of the `Sql` service resource is set directly from the `Mysql::Db` `user`, `password`, and `database` properties.

* To capture information about the node hosting the component, facts describe the state at the time of configuration. In this example the `host` of the `Sql` service resource is set to the fully qualified hostname (`$::fqdn`) of the node running the database.

* To capture information about the node's environment, top-level variables, set by the node classifier, can be accessed. In this example the `$::mysql_host_override` could be set through the node classifier to define the DNS name to use for accessing databases on a specific node.

* Constant values can be used to supply additional information. In this example we specify the capability's `type` as `mysql` to allow its consumers to choose the correct database connector.

* Function calls allow access to a significant part of Puppet's power, from simple `lowercase()` to complex `hiera()` lookups. Note that function calls---like everything else here---are only evaluated during the Puppet run when the producing node's catalog is compiled. In this example the `pick()` function is used to choose between the node classifier supplied value and the fact value, using the latter as the default.

> Note: Since allowing access to a component's properties allows full access to its scope, referencing non-top-level values is technically possible (through default values and function calls) but should not be done in `produces` statements, as they depend on the evaluation order during catalog compilation, and non-top-level values may not actually be available for evaluation.

### Syntax

A `produces` statement has the following syntax:

~~~
ResourceType 'produces' CapabilityType ( '{'
  (AttributeName '=>' Expression ',')*
'}' )?
~~~

The general form of the `produces` statement contains:

* The name of the resource type producing the service resource.
* The `produces` keyword.
* The name of the service resource created.
* An opening curly brace (`{`).
* Any number of attribute and value pairs, each of which consists of:
   * An attribute name, which is a lowercase word with no quotes.
   * A `=>` (called an arrow, “fat comma,” or “hash rocket”).
   * A value, which can have any data type.
   * A trailing comma.
* A closing curly brace (`}`).

### Behavior

* The `ResourceType` can be any normal [resource](/puppet/4.3/reference/lang_resources.html) type, including [classes](/puppet/4.3/reference/lang_classes.html) and [defined types](/puppet/4.3/reference/lang_defined_types.html).
* The `CapabilityType` must be a service resource type.
* The statement does not result in the creation of a service resource; instead, it serves as the blueprint for creating the service resource using the `export` statement.
* Note that for any pair of `(ResourceType, CapabilityType)`, we can have at most one `produces` statement. If there is more than one `produces` statement, Puppet will not know which one to use, and the compiler will flag this as an error.
* The possible names for the `AttributeName` are the names of the attributes of the `CapabilityType`. For any attribute with name `aname` which is not explicitly listed, we act as if the user has written `aname => $aname`.
* The `Expression` for each `AttributeName` is evaluated in a scope that contains top-level variables, including the facts for the node on which the underlying `ResourceType` is being instantiated. In addition, the scope contains bindings for all the parameters/attributes from the instantiation of the underlying `ResourceType`.

## The `consumes` statment

In the next example, the `Wordpress::Instance::App` defined type **consumes** the `Sql` resource capability. Here the attributes for the defined type are derived from the attributes of the service resource it's consuming (for example, the `Wordpress::Instance::App` `db_password` parameter setting comes from the `$password` parameter setting defined in the `Sql` service resource).

~~~
Wordpress::Instance::App consumes Sql {

  # defined_type_attribute  => $capability_attribute,
  db_name       => $name,
  db_host       => $host,
  db_user       => $user,
  db_password   => $password,
}
~~~

Now these two defined types (`Mysql::Db` and `Wordpress::Instance::App`) can exchange data about themselves through the `Sql` service resource. Service resources make it possible to create interfaces between services (potentially across nodes) from existing code without modification. Further, since they provide a level of abstraction at the service level, you could swap out `mysql::db` for `postgresql::server::db`, and the consuming service wouldn't mind in the least.

> **Note**: Properties specified in a component will override any values contained in a service resource.

### Syntax

A `consumes` statement has the following syntax:

~~~
ResourceType 'consumes' CapabilityType ( '{'
  (AttributeName '=>' Expression ',')*
'}' )?
~~~

The general form of the `consumes` statement contains:

* The name of the resource type consuming the service resource.
* The `consumes` keyword.
* The name of the service resource consumed.
* An opening curly brace (`{`).
* Any number of attribute and value pairs, each of which consists of:
   * An attribute name, which is a lowercase word with no quotes.
   * A `=>` (called an arrow, “fat comma,” or “hash rocket”).
   * A value, which can have any data type.
   * A trailing comma.
- A closing curly brace (`}`).

### Behavior

* Similar to `produces`, the `ResourceType` must be a normal [resource](/puppet/4.3/reference/lang_resources.html) type, and the `CapabilityType` must be a service resource type. There can be at most one `consumes` statement for every `(ResourceType, CapabilityType)` pair.
* The `consumes` statement alone does not change how an instance of the `ResourceType` is created. When an instance of the given `ResourceType` is instantiated and uses the `consume` statement, the mappings established by the `consumes` statement are used to fill the attributes/parameters of the resource from the service resource.
* The possible names for the `AttributeName` are all the names of attributes/parameters that `ResourceType` accepts. For each attribute/parameter name `aname` of the `ResourceType`, the `consumes` statement will act as if the statement contained a mapping `aname => $aname` as long as the `CapabilityResource` has an attribute `aname` and the attribute/parameter of the instance of `ResourceType` is not set explicitly in the resource instantiation.  Any attribute/parameter name of `ResourceType` that does not coincide with the name of an attribute of `CapabilityType` will not be affected by the `consumes` statement. It is legal to not use some of the attributes of the `CapabilityType` in the mapping at all.
* The `Expression` for each `AttributeName` is evaluated in a scope that contains top-level variables as well as a variable for each attribute of the `CapabilityResource`, bound to the value of that attribute in the consumed `CapabilityResource`.

### Implicit Parameter Mappings

If the attribute names of both defined types that need to exchange data are identical, the mapping between them within a service resource is automatic.

Let's say you have a defined type called `mysql::db` (that **produces** `Sql`) and a defined type called `wordpress::app` (that **consumes** `Sql`), and both share the attribute name, `$param`.

You can simply express this as:

~~~
mysql::db produces Sql
wordpress::app consumes Sql
~~~





















<!-- Ethnio Activation Code -->
<script type="text/javascript" language="javascript" src="//ethn.io/77154.js" async="true" charset="utf-8"></script>