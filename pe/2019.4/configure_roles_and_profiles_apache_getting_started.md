# Organize webserver configurations with roles and profiles

The roles and profiles method is a reliable way to build reusable, configurable, and refactorable system configurations.

Roles and profiles help you pick out relevant pieces of code from modules and bundle them together to create your own custom set of code for managing things. Profiles are the actual bundles of code. Roles gather profiles together so that you can assign them to nodes. This allows you to efficiently organize your Puppet code.

To learn about roles and profiles by example, follow these instructions to define a profile that configures virtual webhost \(vhost\) to serve the `example.com` website and include a firewall rule. Then, you will create a role to contain the profile, and you'll assign it to a the `apache` node group that you created earlier. This lays down a base structure where, if you had additional websites to serve, you would create additional profiles for them, and those profiles could be separated or combined inside the roles as needed.

Because you are adding a firewall rule, make sure you add the `[puppetlabs/firewall](https://forge.puppet.com/puppetlabs/firewall)` module to your Puppetfile, following the process you used to add the `apache` module.

See [The roles and profiles method](the_roles_and_profiles_method.md#) for more context on how roles and profiles work.

**Parent topic:**[Getting started with Puppet Enterprise](getting_started_pe_overview.md)

## Set up your prerequisites

Before you begin writing content for roles and profiles, you need to create modules to store them in.

1.  Create one module for `profile` and one for `role` directly in your control repo. Do not put them in your Puppetfile.

2.  Make a new directory in the repo named `site`. For example, `/etc/puppetlabs/code/environments/production/site`.

3.  Edit the `environment.conf` file to add `site` to the `modulepath`. This is the place where Puppet looks for module information. For example: `modulepath = site:modules:$basemodulepath`.

4.  Put the `role` and `profile` modules in the `site` directory.


## Write a profile for your Apache vhost

Write a webserver profile that includes rules for your Apache vhost and firewall.

Install the `puppetlabs/apache` module and the `puppetlabs/firewall` module.

1.  In the `profile` module you added, create the following directory:

    -   `manifests/`
        -   `webserver/`
            -   `example.pp`
2.  Paste the following Puppet code into the new `example.pp` file:

    ```
    # /etc/puppetlabs/code/environments/production/site/profile/manifests/webserver/example.pp
    class profile::webserver::example (
      String $content      = "Hello from vhost\\n",
      Array[String] $ports = ['80']
      Array[String] $ips   = ['127.0.0.1','127.0.0.2'],
    ) 
    {
      class { 'apache':
        default_vhost => false,
      }
    {
      apache::vhost { 'example.com':
        port     => $ports,
        ip       => $ips,
        ip_based => true,
        docroot  => '/var/www/html',
      }
      file { '/var/www/html/index.html':
        ensure  => file,
        content => $content,
      }
     firewall { '100 allow http and https access':
        dport  => $ports,
        proto  => tcp,
        action => accept
      }
    }
    ```

    This profile applies custom rules for the `apache::vhost` class that include arrays of `$ports` and `$ips`. The code uses `file` to ensure there is content on the main page of your vhost. Finally, there is a firewall rule that only allows traffic from the port or ports set in the `$ports` array.

    You can add your own code to the profile as needed. Look at the readme and reference sections for the `puppetlabs/apache` and `puppetlabs/firewall` modules in the Forge for more content.


## Set data for the profile

Hiera is a configuration method that allows you to set defaults in your code, or override those defaults in certain circumstances. Use it to fine-tune data within your profile.

Suppose you want to use the custom fact `stage` to represent the deployment stage of the node, which can be `dev`, `test`, or `prod`. For this example, use `dev` and `prod`.

With Hiera structured data, you can set up a four-layer hierarchy that consists of the following:

-   `console_data` for data defined in the console.
-   `nodes/%{trusted.certname}` for per-node overrides.
-   `stage/%{facts.stage}` for setting stage-specific data.
-   `common` for global fallback data.

This structure lets you tune the settings for ports and IPs in each stage.

For example, to configure webservers in the development environment to have a custom message and to use port 8080, you'd create a data file with the following name, location, and code content:

```
# cat /etc/puppetlabs/code/environments/production/data/stage/dev.yaml
---
profile::webserver::example::content: "Hello from dev\n"
profile::webserver::example::ports:
 - '8080'
```

To have webservers in the production environment listen to all interfaces:

```
# cat /etc/puppetlabs/code/environments/production/data/stage/prod.yaml
---
profile::webserver::example::ips:
 - '0.0.0.0'
 - '::'
```

This is the briefest of introductions to all the things you can do with structured data in Hiera. To learn more about setting up hierarchical data, see [Getting started with Hiera](https://puppet.com/docs/puppet/latest/hiera_quick.html#getting_started_with_hiera).

## Write a role for your Apache web server

To write roles, think about what machines you'll be managing and decide what else they need in addition to the `webserver` profile.

Say you want all of the nodes in your `apache` node group to use the profile you just wrote. Suppose also that your organization assigns all machines, including workstations, with a profile called `profile::base`, which manages basic policies and uses some conditional logic to include operating-system-specific configuration.

Write a role that includes both the `base` profile and your `webserver` profile:

```
# /etc/puppetlabs/code/environments/production/site/role/manifests/exampleserver.pp
class role::exampleserver {
  include profile::webserver
  include profile::webserver::example
}
```

You can add more profiles to this role, or create additional roles with more profile configurations based on your needs.

## Assign the role to nodes

Assign the `exampleserver` role to nodes where you want to manage the configuration of the Apache vhost, based on what you wrote in the `webserver::example` profile.

For this example, assume you want to add `role::exampleserver` to all the nodes in the `apache` node group you created.

1.  In the console, click **Classification** and select the `apache` node group.

2.  On the **Configuration** tab, under **Add a new class**, select `role::exampleserver` and click **Add class**.

3.  Commit the change.


Your `apache` node group is managing your Apache vhost based on the rules you coded into your `webserver` profile.

