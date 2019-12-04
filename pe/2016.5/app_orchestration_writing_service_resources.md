---
layout: default
title: "Writing custom service resource types"
canonical: "/pe/latest/app_orchestration_writing_service_resources.html"
---

A service resource type looks very much like any other custom type. Puppet types and providers are written in Ruby. The [custom type documentation](/guides/custom_types.html) has all the information you need for writing custom types.

We recommend some experience writing Ruby code when writing **any** custom types.

Service resources live in a module’s `type` directory and uses the typical path  `<ENVIRONMENT DIRECTORY>/modules/<MODULE NAME>/lib/puppet/type/<SERVICE RESOURCE.rb>`.


A service resource typically adheres to the following format:

~~~ruby
Puppet::Type.newtype :sql, :is_capability => true do
  newparam :name, :namevar => true
  newparam :user
  newparam :password
  newparam :host
  newparam :port do
    defaultto :standard
    newvalues(:standard, /^\d+$/)
  end
  newparam :database
  newparam :type do
    newvalues(:mysql, :postgresql)
  end
end
~~~

Considering this example, it adheres to some of the fundamental rules of custom types (as outlined in the [custom types documentation](/guides/custom_types.html):

* Types are created by calling the `newtype` method on the `Puppet::Type class`.

* The name of the type is the only required argument to `newtype`. The name must be a Ruby symbol, and the name of the file containing the type must match the type’s name.

*  The `newtype` method also requires a block of code, specified with either curly braces (`{ ... }`) or the `do ... end` syntax. This code block will implement the type, and contains all of the properties and parameters. The block will not be passed any arguments.

*  Every type must have at least one mandatory parameter: [the `namevar`](/guides/custom_types.html#namevar).

*  The `is_capability` property lets you specially mark these as environment wide service resources for extraction and injection into catalogs of the producer or consumer. Otherwise, `sql` is just a standard resource that has to be declared on a node.

*  Parameters are defined essentially exactly the same as properties; the only difference between them is that parameters never result in methods being called on providers. To define a new parameter, call the `newparam` method. This method takes the name of the parameter (as a symbol) as its argument, as well as a block of code.

*  [Read more about properties and parameters](/guides/custom_types.html#properties-and-parameters) in custom types.


