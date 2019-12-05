---
title: "Roles and profiles: Designing convenient roles"
---

[intro]: ./r_n_p_intro.html
[example]: ./r_n_p_full_example.html
[profiles]: ./r_n_p_profiles.html
[roles]: ./r_n_p_roles.html

[resource-like]: {{puppet}}/lang_classes.html#include-like-vs-resource-like

Now that you've read our [introduction to roles and profiles][intro] and [complete example][example], and have a good understanding of how the roles and profiles method works, we can discuss some varying approaches to keeping your role classes convenient and simple.

Role classes are much simpler than profiles, but they still require some thought. There are several approaches to building roles, and you must decide which ones are most convenient for you and your team.

## Reminders

### The initial role code

Here's the role we developed in the previous example for managing Jenkins master servers:

``` puppet
class role::jenkins::master {
  include profile::base
  include profile::server
  include profile::jenkins::master
}
```

Unlike our example profile, this is a perfectly realistic role. Profiles handle the majority of your systems' complexity, which allows roles to be much simpler.

### The rules for writing roles

Again, here are the rules for writing role classes:

1. The only thing roles should do is declare profile classes with `include`. Don't declare any component classes or normal resources in a role.

    Optionally, roles can use conditional logic to decide which profiles to use. They can also include other role classes, as long as those roles obey these same rules.
2. Roles should not have any class parameters of their own.
3. Roles should not set class parameters for any profiles. (Those are all handled by data lookup.)
4. The name of a role should be based on your business's _conversational name_ for the type of node it manages.

    This means that if you regularly call a machine a "Jenkins master," it makes sense to write a role named `role::jenkins::master`. But if you call it a "web server," you shouldn't use a name like `role::nginx` --- go with something like `role::web` instead.

### Balancing readability and maintainability

High-quality roles strike a balance between _readability_ and _maintainability._ We mentioned the "Don't Repeat Yourself" (DRY) principle in the section on profiles, and it applies here as well: repeating a section of code in multiple places imposes an extra maintenance burden and increases the chances of errors, but it also makes code easier to read.

In most areas of programming, you should prefer to avoid repetition. But since profiles keep most of the complexity in your Puppet code under control, roles are often more usable if you DO repeat yourself.

## Our recommendation for most people

You should begin with the first approach we describe below ("granular roles") and only deviate from it in small, carefully considered steps.

For most people, the benefit of seeing the entire role in a single file outweighs the maintenance cost of repetition. Later, if you find the repetition burdensome, you can change your approach to reduce it. This might involve combining several similar roles into a more complex role, creating sub-roles that other roles can include, or pushing more complexity into your profiles.

But until then, pay the repetition cost to get the readability win. The simple approach might be all you need.

## First approach: Granular roles

The simplest approach is to make one role per type of node, period. For example, Puppet's Release Engineering team manages some additional resources on their Jenkins masters. With granular roles, we'd have at least two Jenkins master roles. A basic one:

``` puppet
class role::jenkins::master {
  include profile::base
  include profile::server
  include profile::jenkins::master
}
```

...and an RE-specific one:

``` puppet
class role::jenkins::master::release {
  include profile::base
  include profile::server
  include profile::jenkins::master
  include profile::jenkins::master::release
}
```

**Benefits:**

* Readability: By looking at a single class, you can immediately see which profiles make up each type of node.
* Simplicity: Each role is just a linear list of profiles.

**Drawbacks:**

* Role bloat: If you have a lot of only-slightly-different nodes, you'll quickly have a large number of roles.
* Repetition: The two roles above are almost identical, with one difference. If they're two separate roles, it's harder to see how they're related to each other, and updating them can be more annoying.

## Second approach: Conditional logic

Alternately, you can use conditional logic to handle differences between closely-related kinds of nodes:

``` puppet
class role::jenkins::master::release {
  include profile::base
  include profile::server
  include profile::jenkins::master

  if $facts['group'] == 'release' {
    include profile::jenkins::master::release
  }
}
```

**Benefits:**

* You have fewer roles, and they're easy to maintain.

**Drawbacks:**

* Reduced readability... maybe. Conditional logic isn't usually hard to read, especially in a simple case like this, but you might feel tempted to add a bunch of new custom facts to accommodate complex roles. This can make roles much harder to read, because a reader must also know what those facts mean.

    In short, be careful of turning your node classification system inside-out. You might have a better time if you separate the roles and assign them with your node classifier.

## Third approach: Nested roles

Another way of reducing repetition is to let roles include other roles. For example:

``` puppet
class role::jenkins::master {
  # Parent role:
  include role::server
  # Unique classes:
  include profile::jenkins::master
}

class role::jenkins::master::release {
  # Parent role:
  include role::jenkins::master
  # Unique classes:
  include profile::jenkins::master::release
}
```

In this example, we reduce boilerplate by having `role::jenkins::master` include `role::server`. When `role::jenkins::master::release` includes `role::jenkins::master`, it automatically gets `role::server` as well. With this approach, any given role only needs to:

* Include the "parent" role that it most resembles.
* Include the small handful of classes that differentiate it from its parent.

**Benefits:**

* You have fewer roles, and they're easy to maintain.
* Increased visibility in your node classifier.

**Drawbacks:**

* Reduced readability: you have to open more files to see the real content of a role. This isn't much of a problem if you only go one level deep, but it can get wild around three or four.

## Fourth approach: Multiple roles per node

In general, we recommend that you assign only one role to a node. In an infrastructure where nodes usually provide one primary service, that's the best way to work.

However, if your nodes tend to provide more than one primary service, it can make sense to assign multiple roles.

For example, say you have a large application that is usually composed of an application server, a database server, and a web server. To enable lighter-weight testing during development, you've decided to provide an "all-in-one" node type to your developers. You could do this by creating a new `role::our_application::monolithic` class, which includes all of the profiles that compose the three normal roles, but you might find it simpler to use your node classifier to assign all three roles (`role::our_application::app`, `role::our_application::db`, and `role::our_application::web`) to those all-in-one machines.

**Benefits:**

* You have fewer roles, and they're easy to maintain.

**Drawbacks:**

* There's no actual "role" that describes your multi-purpose nodes; instead, the source of truth for what's on them is spread out between your roles and your node classifier, and you must cross-reference to understand their configurations. This reduces readability.
* The normal and all-in-one versions of a complex application are likely to have other subtle differences you need to account for, which might mean making your "normal" roles more complex. It's possible that making a separate role for this kind of node would _reduce_ your overall complexity, even though it increases the number of roles and adds repetition.


## Fifth approach: Super profiles

Since profiles can already include other profiles, you can decide to enforce an additional rule at your business: all profiles must include any other profiles needed to manage a complete node that provides that service.

For example, our `profile::jenkins::master` class could include both `profile::server` and `profile::base`, and you could manage a Jenkins master server by directly assigning `profile::jenkins::master` in your node classifier. In other words, a "main" profile would do all the work that a role usually does, and the roles layer would no longer be necessary.

**Benefits:**

* The chain of dependencies for a complex service can be more clear this way.
* Depending on how you conceptualize code, this can be easier in a lot of ways!

**Drawbacks:**

* Loss of flexibility. This reduces the number of ways in which your roles can be combined, and reduces your ability to use alternate implementations of dependencies for nodes with different requirements.
* Reduced readability, on a much grander scale. Like with nested roles, you lose the advantage of a clean, straightforward list of what a node consists of. Unlike nested roles, you also lose the clear division between "top-level" complete system configurations (roles) and "mid-level" groupings of technologies (profiles). Not every profile makes sense as an entire system, so you'll need some way to keep track of which profiles are the top-level ones.

    Some people really find continuous hierarchies easier to reason about than sharply divided layers. If everyone in your organization is on the same page about this, a "profiles and profiles" approach might make sense. But we strongly caution you against it unless you're very sure; for most people, a true roles and profiles approach works better. Try the well-traveled path first.

## Sixth approach: Building roles in the node classifier

Instead of building roles with the Puppet language and then assigning them to nodes with your node classifier, you might find your classifier flexible enough to build roles directly. For example, you might create a "Jenkins masters" group in the PE console and assign it the `profile::base`, `profile::server`, and `profile::jenkins::master` classes, doing much the same job as our basic `role::jenkins::master` class.

> **Important:** If you're doing this, make sure you don't set parameters for profiles in the classifier! Continue to use Hiera / Puppet lookup to configure profiles.
>
> This is because profiles are allowed to include other profiles, which interacts badly with the [resource-like][] behavior that node classifiers use to set class parameters.

**Benefits:**

* Your node classifier becomes much more powerful, and can be a central point of collaboration for managing nodes.
* Increased readability: A node's page in the PE console displays the full content of its role, without having to cross-reference with manifests in your `role` module.

**Drawbacks:**

* Loss of flexibility. The Puppet language's conditional logic is often more flexible and convenient than most node classifiers, including the PE console.
* Your roles are no longer in the same code repository as your profiles, and it's more difficult to make them follow the same code promotion processes.
