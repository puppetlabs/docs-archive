# The roles and profiles method

The *roles and profiles* method is the most reliable way to build reusable, configurable, and refactorable system configurations.

It's not a straightforward recipe: you must think hard about the nature of your infrastructure and your team. It's also not a final state: expect to refine your configurations over time. Instead, it's an approach to *designing your infrastructure's interface* — sealing away incidental complexity, surfacing the significant complexity, and making sure your data behaves predictably.

## Building configurations without roles and profiles

Without roles and profiles, people typically build system configurations in their node classifier or main manifest, using Hiera to handle tricky inheritance problems. A standard approach is to create a group of similar nodes and assign classes to it, then create child groups with extra classes for nodes that have additional needs. Another common pattern is to put everything in Hiera, using a very large hierarchy that reflects every variation in the infrastructure.

If this works for you, then it works! You might not need roles and profiles. But most people find direct building gets difficult to understand and maintain over time.

**Parent topic:**[Designing system configs: roles and profiles](designing_system_configs_roles_and_profiles.md)

## Configuring roles and profiles

Roles and profiles are *two extra layers of indirection* between your node classifier and your component modules.

The roles and profiles method separates your code into three levels:

-   Component modules — Normal modules that manage one particular technology, for example puppetlabs/apache.
-   Profiles — Wrapper classes that use multiple component modules to configure a layered technology stack.
-   Roles — Wrapper classes that use multiple profiles to build a complete system configuration.

These extra layers of indirection might seem like they add complexity, but they give you a space to build practical, business-specific interfaces to the configuration you care most about. A better interface makes hierarchical data easier to use, makes system configurations easier to read, and makes refactoring easier.

![](roles_and_profiles_overview.png)

In short, from top to bottom:

-   Your node classifier assigns one role class to a group of nodes. The role manages a whole system configuration, so no other classes are needed. The node classifier does not configure the role in any way.
-   That role class declares some profile classes with `include`, and does nothing else. For example:

    ```
      class role::jenkins::master {
        include profile::base
        include profile::server
        include profile::jenkins::master
      }           
    ```


-   Each profile configures a layered technology stack, using multiple component modules and the built-in resource types. \(In the diagram, `profile::jenkins::master` uses puppet/jenkins, puppetlabs/apt, a home-built backup module, and some `package` and `file` resources.\)
-   Profiles can take configuration data from the console, Hiera, or Puppet lookup. \(In the diagram, three different hierarchy levels contribute data.\)
-   Classes from component modules are always declared via a profile, and never assigned directly to a node.
    -   If a component class has parameters, you specify them in the profile*;* never use Hiera or Puppet lookup to override component class params.

## Rules for profile classes

There are rules for writing profile classes.

-   Make sure you can safely `include` any profile multiple times — don't use resource-like declarations on them.
-   Profiles can `include` other profiles.
-   Profiles own all the class parameters for their component classes. If the profile omits one, that means you definitely want the default value; the component class shouldn't use a value from Hiera data. If you need to set a class parameter that was omitted previously, refactor the profile.
-   There are three ways a profile can get the information it needs to configure component classes:

    -   If your business always uses the same value for a given parameter, hardcode it.
    -   If you can't hardcode it, try to compute it based on information you already have.
    -   Finally, if you can't compute it, look it up in your data. To reduce lookups, identify cases where multiple parameters can be derived from the answer to a single question.
    This is a game of trade-offs. Hardcoded parameters are the easiest to read, and also the least flexible. Putting values in your Hiera data is very flexible, but can be very difficult to read: you might have to look through a lot of files \(or run a lot of lookup commands\) to see what the profile is actually doing. Using conditional logic to derive a value is a middle-ground. Aim for the most readable option you can get away with.


## Rules for role classes

There are rules for writing role classes.

-   The only thing roles should do is declare profile classes with `include`. Don't declare any component classes or normal resources in a role.

    Optionally, roles can use conditional logic to decide which profiles to use.

-   Roles should not have any class parameters of their own.
-   Roles should not set class parameters for any profiles. \(Those are all handled by data lookup.\)
-   The name of a role should be based on your business's *conversational name* for the type of node it manages.

    This means that if you regularly call a machine a "Jenkins master," it makes sense to write a role named `role::jenkins::master`. But if you call it a "web server," you shouldn't use a name like `role::nginx` — go with something like `role::web` instead.


## Methods for data lookup

Profiles usually require some amount of configuration, and they must use data lookup to get it.

This profile uses the automatic class parameter lookup to request data.

```
# Example Hiera data
profile::jenkins::jenkins_port: 8000
profile::jenkins::java_dist: jre
profile::jenkins::java_version: '8'
 
# Example manifest
class profile::jenkins (
  Integer $jenkins_port,
  String  $java_dist,
  String  $java_version
) {
# ...
```

This profile omits the parameters and uses the `lookup` function:

```
class profile::jenkins {
  $jenkins_port = lookup('profile::jenkins::jenkins_port', {value_type => String, default_value => '9091'})
  $java_dist    = lookup('profile::jenkins::java_dist',    {value_type => String, default_value => 'jdk'})
  $java_version = lookup('profile::jenkins::java_version', {value_type => String, default_value => 'latest'})
  # ...
```

In general, class parameters are preferable to lookups. They integrate better with tools like Puppet strings, and they're a reliable and well-known place to look for configuration. But using `lookup` is a fine approach if you aren't comfortable with automatic parameter lookup. Some people prefer the full lookup key to be written in the profile, so they can globally grep for it.

