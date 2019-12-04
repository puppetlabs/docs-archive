# Availability tests

Availability tests are custom resource providers for testing service resources when you configure applications. They can be used to ensure your nodes are correctly configured and services are running before Puppet configures dependent services on another node.

## Example service resource with built-in availability test

When you create service resources, you want to include availability tests when necessary.

The following example shows two example availability tests. These tests are based on [puppet-healthcheck](https://github.com/voxpupuli/puppet-healthcheck).

-   **TCP**
    -   The provider is called `tcp`.
    -   This check performs a simple ping against a host and port to determine if the service is up.
-   **PostgreSQL**
    -   The provider is called `pg`.
    -   This check logs into specified database to do a simple [`SELECT 1`](https://www.postgresql.org/docs/9.6/static/sql-select.html) to determine if the database is up.
    -   This check requires that you install the [postgres-pr gem](https://rubygems.org/gems/postgres-pr/versions/0.7.0) on the agent that runs the check.

In the following example, the component \(`My::Db`\) produces a service resource \(`Sql`\), and passes the `tcp` provider from the component into the service resource.

```
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
```

Based on this example, the following parameters from the `sql` type dictate the behavior of the availability test providers:

-   `host`
    -   The host to run the test on.
    -   Default is `127.0.0.1`.
-   `port`

    -   The port to run the test on.
    -   Defaults is `80` or `5432`, depending on it's a TCP or PSQL test.
-   `timeout`

    -   How long to run the test.
    -   Defaults is `60` seconds.
-   `ping_interval`

    -   How long to wait between tests.
    -   Defaults is `1` second.
-   `user`

    -   The user to log into the database with.
    -   There is no default value.
-   `password`

    -   The password to log into the database with.
    -   There is no default value.
-   `database`

    -   The database to connect to.
    -   There is no default value.

## Writing availability tests

You can write your own availability tests as needed. To write an availability check, you need to create a custom type and a custom provider.

### Writing the type

The type needs to be defined as a service resource. The following example shows the basic format of a service resource.

```ruby
Puppet::Type.newtype :mytype, :is_capability => true do
  newparam :type1
  newparam :type2
  newparam :type3
end
```

Here, you add whatever parameters this service resource needs to function.

In the module that defines your application, this service resource type would be located at `lib/puppet/type/<YOUR TYPE>/<YOUR TYPE>.rb`.

### Writing the provider

The corresponding provider is what performs the availability test. Most parts of the provider are dependent on how you want the test to function. However, there two required methods:

-   `exists?`: The method with which you call your availability test code. This is also where you can pass in any parameter from your type to your availability test method.

-   `create`: The method on which you can raise a `Puppet::Error`. This is called only if the `exists?` method returns `false`.


The following example shows the format of the provider portion of a basic availability test. As noted in the example, the default behavior of the test is to pass all parameters from the service resource to the availability test. You can also pass specific parameters, as shown in the example.

\(Remember, the service resource type would be located at `lib/puppet/provider/<YOUR TYPE>/<YOUR TYPE>.rb`.\)

```ruby
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
```

### Ensuring test runs only on consuming node

An availability test runs **only** on the node consuming the service resource. For example, if you have an API node and a database node, and the database node produces a `Sql` capability resource consumed by the API node, the availability test runs only on the API node after the database node has been configured.

The availability test also runs before any other resources are configured during the Puppet run. If it fails, all dependencies are skipped. If it successfully makes a connection, the Puppet run moves on and configures the remaining parts of the application.

To ensure the availability test runs only on the consuming node, you must include the following code on your health check method:

```ruby
def my_availability_test
  return true if resource.tags.grep(/^producer:/).any?
  # rest of the availability test code
end
```

**Related links**

-   Custom type and a provider documentation

-   Working examples


See the following provider directories in the puppet-healthcheck module for working examples.

**Related information**  


[Writing custom service resource types](writing_custom_service_resource_types.md)

