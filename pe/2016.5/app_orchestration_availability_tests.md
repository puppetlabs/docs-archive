---
layout: default
title: "Availability tests"
canonical: "/pe/latest/app_orchestration_availability_tests.html"
---

**Availability tests** are [custom resource providers](/guides/custom_types.html#providers) for testing service resources when you configure applications. They can be used to ensure your nodes are correctly configured and services are running before Puppet configures dependent services on another node.

The following is based on two example availability tests. These tests are based on [**puppet-healthcheck**](https://github.com/puppet-community/puppet-healthcheck).

- **TCP**
   + The provider is called `tcp`.
   + This check performs a simple ping against a host and port to determine if the service is up.

- **PostgreSQL**
   + The provider is called `pg`.
   + This check logs into specified database to do a simple [`SELECT 1`](http://www.postgresql.org/docs/current/static/sql-select.html) to determine if the database is up.
   + This check requires that you install the  [postgres-pr gem](https://rubygems.org/gems/postgres-pr/versions/0.7.0) on  the agent that will run the check.

## Example service resource with built-in availability test

In the following example, the component (My::Db) produces a service resource (`Sql`), and passes the `tcp` provider from the component into the service resource.

~~~

define my::db(
  $user = 'myuser',
  $password = 'mypassword',
  $host = 'myhost',
  $port = '5432',
  $database = 'mydb',
  $provider = 'tcp',
) {

}

My::Db produces Sql {
    user      => $user,
    password  => $password,
    host      => $host,
    port      => $port,
    database  => $dbname,
    provider  => $provider,
}
~~~

Based on this example, the following parameters from the `sql` type dictate the behavior of the availability test providers:

- `host`
  + The host to run the test on.
  + Default is `127.0.0.1`.

- `port`
  + The port to run the test on.
  + Defaults is `80` or `5432`, depending on it's a TCP or PSQL test.

- `timeout`
  + How long to run the test.
  + Defaults is `60` seconds.

- `ping_interval`
  + How long to wait between tests.
  + Defaults is `1` second.

- `user`
  + The user to log into the database with.
  + There is no default value.

- `password`
  + The password to log into the database with.
  + There is no default value.

- `database`
  + The database to connect to.
  + There is no default value.

## Availability test behavior

This test will run **only** on the node consuming the service resource. For example, if you have an API node and a databse node, and the database node produces a Sql capability resource consumed by the API node, the availability test will only run on the API node after the database node has been configured.

The availability test will also run before any other resources are configured during the Puppet run. If it fails, all dependencies will be skipped. If it successfully makes a connection, the Puppet run will move on and configure the remaining parts of the application.

## Writing availability tests

To write an availability check, you need to create a [type and a provider](/guides/custom_types.html#types-and-providers).

### The type

The type needs to be defined as a [service resource](app_orchestration_writing_service_resources.html). The following example shows the basic format of a service resource. 

~~~ruby
Puppet::Type.newtype :mytype, :is_capability => true do
  newparam :type1
  newparam :type2
  newparam :type3
end
~~~

Here, you add whatever parameters this service resource needs to function.

In the module that defines your application, this service resource type would be located at `lib/puppet/type/<YOUR TYPE>/<YOUR TYPE>.rb`. 

### The provider

The corresponding provider is what performs the availability test. Most parts of the provider are dependent on how you want the test to function. However, there two required methods:

- `exists?`: The method with which you call your availability test code. This is also where you can pass in any parameter from your type to your availability test method.

- `create`: The method on which you can raise a `Puppet::Error`. This will only be called if the `exists?` method returns `false`.

The following example shows the format of the provider portion of a basic availability test. As noted in the example, the default behavior of the test is to pass all parameters from the service resource to the availability test. You can also pass specific parameters, as shown in the example. 

(Remember, the service resource type would be located at `lib/puppet/provider/<YOUR TYPE>/<YOUR TYPE>.rb`.) 

~~~ruby
require 'puppet/provider'

class Puppet::Provider::Tcp < Puppet::Provider
  def my_availability_test
    # availability test code
    Puppet::Info "Doing an availability test"
    true
  end

  # default, passes all parameters from the service resource to the availability test 
  # or the commented out code: passing specific parameters to the test
  def exists?
    my_availability_test
    # my_availability_test(resource[:type1], resource[:type2], resource[:type3])
  end

  def create
    raise Puppet::Error, "The availability test failed"
  end
end
~~~


## The consuming versus the producing node

As noted in [Availability Test Behavior](#availability-test-behavior), the test runs on the consuming node. To ensure the availability test only runs on the consuming node, you must include the following code on your health check method: 

>**Note**: if you don't include this code, the availability test will run on both the consuming and producing node.

~~~ruby
def my_availability_test
  return true if resource.tags.grep(/^producer:/).any?
  # rest of the availability test code
end
~~~

## Working examples

See the following provider directories in the [puppet-healthcheck module](https://github.com/puppet-community/puppet-healthcheck/tree/master/lib/puppet/provider) for working examples.
