# Roles and profiles example

This example demonstrates a complete roles and profiles workflow. Use it to understand the roles and profiles method as a whole. Additional examples show how to design advanced configurations by refactoring this example code to a higher level of complexity.

## Configure Jenkins master servers with roles and profiles

Jenkins is a continuous integration \(CI\) application that runs on the JVM. The Jenkins master server provides a web front-end, and also runs CI tasks at scheduled times or in reaction to events.

### About this task

In this example, we manage the configuration of Jenkins master servers.

### Set up your prerequisites

If you're new to using roles and profiles, do some additional setup before writing any new code.

#### Procedure

1.  Create two modules: one named `role`, and one named `profile`.

    If you deploy your code with Code Manager or r10k, put these two modules in your control repository instead of declaring them in your Puppetfile, because Code Manager and r10k reserve the `modules` directory for their own use.

    1.  Make a new directory in the repo named `site`.

    2.  Edit the `environment.conf` file to add `site` to the `modulepath`. \(For example: `modulepath = site:modules:$basemodulepath`\).

    3.  Put the `role` and `profile` modules in the `site` directory.

2.  Make sure Hiera or Puppet lookup is set up and working, with a hierarchy that works well for you.


### Choose component modules

For our example, we want to manage Jenkins itself. The standard module for that is `rtyler/jenkins`.

#### About this task

Jenkins requires Java, and the `rtyler` module can manage it automatically. But we want finer control over Java, so we're going to disable that. So, we need a Java module, and `puppetlabs/java` is a good choice.

That's enough to start with. We can refactor and expand when we have those working.

#### Results

To learn more about these modules, see [rtyler/jenkins](https://forge.puppet.com/rtyler/jenkins?_ga=2.134232694.623882382.1502209414-2028041969.1502209414), [puppetlabs/java](https://forge.puppet.com/puppetlabs/java?_ga=2.126344074.623882382.1502209414-2028041969.1502209414).

### Write a profile

From a Puppet perspective, a profile is just a normal class stored in the `profile` module.

#### Procedure

1.  Make a new class called `profile::jenkins::master`, located at ...`/profile/manifests/jenkins/master.pp`, and fill it with Puppet code.

    ```
    # /etc/puppetlabs/code/environments/production/site/profile/manifests/jenkins/master.pp
    class profile::jenkins::master (
      String $jenkins_port = '9091',
      String $java_dist    = 'jdk',
      String $java_version = 'latest',
    ) {
    
      class { 'jenkins':
        configure_firewall => true,
        install_java       => false,
        port               => $jenkins_port,
        config_hash        => {
          'HTTP_PORT'    => { 'value' => $jenkins_port },
          'JENKINS_PORT' => { 'value' => $jenkins_port },
        },
      }
    
      class { 'java':
        distribution => $java_dist,
        version      => $java_version,
        before       => Class['jenkins'],
      }
    }
    ```

    This is pretty simple, but is already benefiting us: our interface for configuring Jenkins has gone from 30 or so parameters on the Jenkins class \(and many more on the Java class\) down to three. Notice that we’ve hardcoded the `configure_firewall` and `install_java` parameters, and have reused the value of `$jenkins_port` in three places.


#### Results

**Related information**  


[Rules for profile classes](the_roles_and_profiles_method.md#)

[Methods for data lookup](the_roles_and_profiles_method.md#)

### Set data for the profile

#### About this task

Let’s assume the following:

-   We use some custom facts:

    -   `group`: The group this node belongs to. \(This is usually either a department of our business, or a large-scale function shared by many nodes.\)

    -   `stage`: The deployment stage of this node \(dev, test, or prod\).

-   We have a five-layer hierarchy:

    -   console\_data for data defined in the console.

    -   `nodes/%{trusted.certname}` for per-node overrides.

    -   `groups/%{facts.group}/%{facts.stage}` for setting stage-specific data within a group.

    -   `groups/%{facts.group}` for setting group-specific data.

    -   `common` for global fallback data.

-   We have a few one-off Jenkins masters, but most of them belong to the `ci` group.

-   Our quality engineering department wants masters in the `ci` group to use the Oracle JDK, but one-off machines can just use the platform’s default Java.

-   QE also wants their prod masters to listen on port 80.


#### Procedure

1.  Set appropriate values in the data, using either Hiera or configuration data in the console.

    **Tip:** In most cases, setting configuration data in Hiera is the more scalable and consistent method, but there are some cases where the console is preferable. Use the console to set configuration data if:

    -   You want to override Hiera data. Data set in the console overrides Hiera data when configured as recommended.
    -   You want to give someone access to set or change data and they don’t have the skill set to do it in Hiera.
    -   You simply prefer the console user interface.

    ```
    # /etc/puppetlabs/code/environments/production/data/nodes/ci-master01.example.com.yaml
     # --Nothing. We don't need any per-node values right now.
    
     # /etc/puppetlabs/code/environments/production/data/groups/ci/prod.yaml
     profile::jenkins::master::jenkins_port: '80'
    
     # /etc/puppetlabs/code/environments/production/data/groups/ci.yaml
     profile::jenkins::master::java_dist: 'oracle-jdk8'
     profile::jenkins::master::java_version: '8u92'
    
     # /etc/puppetlabs/code/environments/production/data/common.yaml
     # --Nothing. Just use the default parameter values.
    ```


### Write a role

To write roles, we consider the machines we’ll be managing and decide what else they need in addition to that Jenkins profile.

Our Jenkins masters don’t serve any other purpose. But we have some profiles \(code not shown\) that we expect every machine in our fleet to have:

-   `profile::base` must be assigned to every machine, including workstations. It manages basic policies, and uses some conditional logic to include OS-specific profiles as needed.

-   `profile::server` must be assigned to every machine that provides a service over the network. It makes sure ops can log into the machine, and configures things like timekeeping, firewalls, logging, and monitoring.


So a role to manage one of our Jenkins masters should include those classes as well.

```
class role::jenkins::master {
  include profile::base
  include profile::server
  include profile::jenkins::master
}
```

**Related information**  


[Rules for role classes](the_roles_and_profiles_method.md#)

### Assign the role to nodes

Finally, we assign `role::jenkins::master` to every node that acts as a Jenkins master.

Puppet has several ways to assign classes to nodes, so use whichever tool you feel best fits your team. Your main choices are:

-   The console node classifier, which lets you group nodes based on their facts and assign classes to those groups.

-   The main manifest which can use node statements or conditional logic to assign classes.

-   [Hiera](https://puppet.com/docs/puppet/6.5/hiera.html) or Puppet lookup — Use [the `lookup` function](https://puppet.com/docs/puppet/6.5/hiera_automatic.html#puppet-lookup) to do a unique array merge on a special `classes` key, and pass the resulting array to the `include` function.

```
# /etc/puppetlabs/code/environments/production/manifests/site.pp
lookup('classes', {merge => unique}).include
```


