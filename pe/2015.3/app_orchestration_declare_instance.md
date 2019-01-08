---
layout: default
title: "Declaring Application Instances"
canonical: "/pe/latest/app_orchestration_declare_instance.html"
---

The application instance is the instantiation of an application model. An application can be instantiated any number of times with a variety of component-to-node assignment combinations---from all components in an application assigned to one node, to the same components spread across several nodes in your infrastructure.

When you declare an application instance, you:

1. [Add the declaration to `site.pp`](#declaring-application-instances-in-sitepp).
2. [Assign components to nodes](assigning-components-to-nodes).
3. [Add service resource mapping statements](#adding-service-resource-mapping-statements).

## Declaring Application Instances in Main Manifest

You declare application instances in an environment's [main manifest (`site.pp`)](/puppet/4.3/reference/dirs_manifest.html).

Application instances go inside a `site { }` block, which allows allows applications to be evaluated separately from node-level resources. You have one `site { }` per environment, and you declare all application instances for an environment within it.

Within the `site { }` block, applications are declared like defined types. They can be declared any number of times, but their type and title combination must be unique within an environment.

The following section provides some [example application instance declarations](#example-application-instance-declarations).

[Node Assignment Syntax](#node-assignment-syntax) explains the node mapping statements, a crucial step in instantiating your application.

## Example Application Instance Declarations

The following example shows how you’d declare an instance of an application with two or more components. In this case, `database.vm` is hosting the  database while `appserver.vm` is hosting the app server.

>**Notes**: In the following examples, the `$codedir` variable refers to [Puppet’s code and data directory](/puppet/4.3/reference/dirs_codedir.html).
>
> Component titles are taken from application definitions. In these examples, we refer to them as `<COMPONENT TITLE>`.
>
> In addition, application instances cannot use static values for parameters. In these examples, we use `<USER NAME>` and `<PASSWORD>`.

~~~
$codedir/environments/production/manifests/site.pp

site {
  lamp { 'stack':
    db_user      => <USER NAME>,
    db_password  => '<PASSWORD>',
    nodes           => {
      Node['database.vm'] => Lamp::Db[<COMPONENT TITLE>],
      Node['appserver.vm'] => Lamp::App[<COMPONENT TITLE>],
        },
      }
}
~~~

Alternatively, you can have a single node application with multiple components on that node, as shown in the following application declaration example. In this case, `example.vm` is hosting both the MSQL database and the Wordpress application.

~~~
$codedir/environments/production/manifests/site.pp

site {
  lamp { 'dev':
    db_user      => <USER NAME>,
    db_password  => '<PASSWORD>',
    nodes           => {
      Node['example.vm'] => [ Lamp::Db[<COMPONENT TITLE>],Lamp::Web[<COMPONENT TITLE>]],
        },
      }
}
~~~

## Node Assignment Syntax

A crucial step in declaring application instances is assigning application components to nodes. The mapping is accomplished with the `nodes` attributes in the application instance declaration in `site.pp`.

In this example, `example01.node.cert` is assigned two components, and `example02.node.cert` is assigned one.

~~~
nodes       => {
  Node['example01.node.cert'] => [ Component['one'], Component['two'] ],
  Node['example02.node.cert']    => Component['three'],
},

~~~

The general form of the node mapping statement is structured as follows:

* The `nodes` attribute.
   * `Node` followed by brackets (`[ ]`) that contain the [certname](/references/4.3.latest/configuration.html#certname) of the node you're mapping the component to.
   * A `=>` (called an arrow, “fat comma,” or “hash rocket”).
   * An opening bracket (`[`) followed by a space, followed by an array of components, with each component followed by brackets (`[ ]`) containing the component title, and then an additional space. Do not put a space between the component and the component's name. The title of the component declared in the application instance must match the title of the component given in the application manifest. (The defined type [`$name` variable](/puppet/4.3/reference/lang_defined_types.html#title-and-name) is useful in the application definition **but cannot be used in application instances**.)
   * A closing (`]`) bracket.
   * A comma.

> **Important**: If you do not map your components to nodes, they will not show up in the environment graph and therefore not be usable with Puppet Application Orchestration.

### Errors to Avoid When Assigning Components to Nodes

#### Do Not Map A Single Component to Multiple Nodes

If you attempt to map the same component to multiple nodes, as shown in the following example, your application will not be properly configured.

~~~
lamp { 'example_instance':
  wp_db_user      => <USER NAME>,
  wp_db_password  => '<PASSWORD>',
  nodes           => {
    Node['database.vm'] => [ Lamp::Db[<COMPONENT TITLE>],
    Node['example.vm'] => [ Lamp::Db[<COMPONENT TITLE>],Lamp::Web[<COMPONENT TITLE>]]
  }
}
~~~

Note that you cannot use a variable (such as `$name`) to declare the components. Instead, follow this example to map the components to the nodes:

~~~
elk { 'split':
  nodes       => {
    Node['centos7-1.vm'] => [ Elk::Es['split'] ],
    Node['centos7-2.vm'] => [ Elk::Kibana['split'] ]
  }
}
~~~

## Adding Service Resource Mapping Statements

Add service resource mapping statements either in your site manifest (`site.pp`) or in modules that contain service resources.

### In the Site Manifest

Declaring service resource mapping statements in your site manifest (`site.pp`)  allows you to use modules (such as those from the Puppet Forge) that are not wired for application management alongside instantiated applications.

~~~
$codedir/environments/production/manifests/site.pp

site {
  lamp { 'example_instance':
    wp_db_user      => <USER NAME>,
    wp_db_password  => '<PASSWORD>',
    nodes           => {
      Node['database.vm'] => [Lamp::Db[<COMPONENT TITLE>]],
      Node['appserver.vm'] => [Lamp::Web[<COMPONENT TITLE>]]
    }
  }

 Lamp::Db produces Sql {
  user      => $user,
  password  => $password,
  host      => pick($::mysql_host_override, $::fqdn),
  #port     => not used here, will default as described in the definition
  database  => $dbname,
  type      => 'mysql',
 }
}
~~~

### In Modules With Service Resources

You can add service resource mappings to modules that include service resources. Declare the mappings in the manifest that conforms to autoloader rules, typically outside of the class/defined-resource type.

~~~
$modulepath/mysql/manifests/db.pp

define lamp::db {
   ....
}

Lamp::Db produces Sql {
  user      => $user,
  password  => $password,
  host      => pick($::mysql_host_override, $::fqdn),
  #port     => not used here, will default as described in the definition
  database  => $dbname,
  type      => 'mysql',
}
~~~

## Validating Application Orchestration Code

When you're ready to validate Puppet code you intend to use as application orchestration code, you need to add the `--app_management` flag to the parser command. The full command to run is `puppet parser validate --app_management`.

## Next Steps

- Start writing some service resources. Refer to the documentation on [producing and consuming services resources](./app_orchestration_produce_consume.html) and [writing service resources](./app_orchestration_writing_service_resources.html).
- After you instantiate an application, use the [Puppet Orchestrator](./orchestrator_intro.html#puppet-orchestrator-introduction) to run Puppet to configure your application.


