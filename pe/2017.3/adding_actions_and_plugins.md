# Adding actions and plugins to PE

You can extend PE's MCollective engine by adding new actions. Actions are distributed in agent plugins, which are bundles of several related actions. You can write your own agent plugins \(or download ones created by other people\), and use PE to install and configure them on your nodes.

## Agent plugin components

Agent plugins consist of two parts: a `.rb` file containing the MCollective agent code and `a.ddl` file containing a description of plugin's actions, inputs, and outputs.

Every agent node that will be using this plugin needs both files. The Puppet master node and console node each need the `.ddl` file.

**Note:** Additionally, some agent plugins may be part of a bundle of related plugins, which may include new subcommands, data plugins, and more. A full list of plugin types and the nodes they should be installed on is available here. Note that "servers" refers to PE agent nodes and "clients" refers to the Puppet master and console nodes.

## Agent plugin distribution

Not every agent node needs to use every plugin—MCollective is built to gracefully handle an inconsistent mix of plugins across nodes.

This means you can distribute special-purpose plugins to only the nodes that need them, without worrying about securing them on irrelevant nodes. Nodes that don't have a given plugin will ignore its actions, and you can also filter commands by the list of installed plugins.

## Getting new plugins

You can write your own MCollective plugins, or download ones written by other people.

### Downloading agent plugins

There isn't a central repository of agent plugins, but there are several good places to start looking:

-   [A list of the plugins released by Puppet is available here.](https://docs.puppet.com/mcollective/plugin_directory/index.html)
-   If you use Nagios, [the NRPE plugin](https://github.com/puppetlabs/mcollective-nrpe-agent) is a good first plugin to install.
-   [Searching GitHub for “mcollective agent”](https://github.com/search?q=mcollective+agent) will turn up many plugins, including ones for `vmware_tools`, libvirt, junk filters in `iptables`, and more.

### Writing agent plugins

Most people who use MCollective heavily will want custom actions tailored to the needs of their own infrastructure. You can get these by writing new agent plugins in Ruby.

The MCollective documentation has instructions for writing agent plugins:

-   [Writing agent plugins](https://docs.puppet.com/mcollective/simplerpc/agents.html)
-   [Writing DDL files](https://docs.puppet.com/mcollective/reference/plugins/ddl.html)
-   [Aggregating replies for better command line interfaces](https://docs.puppet.com/mcollective/reference/plugins/aggregate.html)

Additionally, you can learn a lot by reading the code of PE's built-in plugins. These are located in the `/opt/puppet/libexec/mcollective/mcollective/` directory on any \*nix node.

### Overview of plugin installation process

Since actions need to be installed on many nodes, and since installing or upgrading an agent should always restart the `mcollective` service, you should use Puppet to install agent plugins.

**Note:** The full MCollective documentation includes a guide to installing plugins. Puppet Enterprise users must use the "copy into libdir" installation method. The remainder of this topic goes into more detail about using this method with Puppet Enterprise.

To install a new agent plugin, you must write a Puppet module that does the following things:

-   On agent nodes: copy the plugin's `.rb` and `.ddl` files into the `mcollective/agent` subdirectory of MCollective's libdir. This directory's location varies between \*nix and Windowsnodes.
-   On the console and Puppet master nodes: if you will not be installing this plugin on *every* agent node, copy the plugin's `.ddl` file into the `mcollective/agent` subdirectory of MCollective's libdir.
-   If there are any other associated plugins included \(such as data or validator plugins\), copy them into the proper libdir subdirectories on agent nodes, the console node, and the Puppet master node.
-   If any of these files change, restart the `mcollective` service, which is managed by the `puppet_enterprise_mcollective` module.

To accomplish these, you will need to write some limited interaction with the `puppet_enterprise_mcollective` module, which is part of PE's implementation.

### Step 1: Create a module for your plugin\(s\)

You have several options for laying this out:

-   **One class for all of your custom plugins.** This works fine if you have a limited number of plugins and will be installing them on every agent node.
-   **One module with several classes for individual plugins or groups of plugins.** This is good for installing certain plugins on only some of your agent nodes --- you can split specialized plugins into a pair of `mcollective_plugins::<name>::agent` and `mcollective_plugins::<name>::client` classes, and assign the former to the affected agent nodes and the latter to the console and Puppet master nodes.
-   **A new module for each plugin.** This is maximally flexible, but can sometimes get cluttered.

After the module is created, **put the plugin files into its `files/` directory.**

### Step 2: Create relationships and set variables

For any class that will be installing plugins **on agent nodes,** you should put the following four lines near the top of the class definition:

```ruby
    Class['puppet_enterprise::mcollective::server::plugins'] -> Class[$title] ~> Service['mcollective']
    include puppet_enterprise::params
    $plugin_basedir = "${puppet_enterprise::params::mco_plugin_userdir}/mcollective"
    $mco_etc        = $puppet_enterprise::params::mco_etc
```

This will do the following:

-   Ensure that the necessary plugin directories already exist before we try to put files into them. \(In certain cases, these directories are managed by variables in the `puppet_enterprise::params` class.\)
-   Restart the `mcollective` service whenever new plugins are installed or upgraded. \(This service resource is declared in the `puppet_enterprise::mcollective::server` class.\)
-   Set variables that will correctly refer to the plugins directory and configuration directory on both \*nix and Windows nodes.

**Note:** The `Class[$title]` notation seen above is a resource reference to the class that contains this statement; it uses the `$title` variable, which always contains the name of the surrounding container.

### Step 3: Put files in place

First, set file defaults: all of these files should be owned by root and only writable by root \(or the Administrators user, on Windows\). The `puppet_enterprise` module has helpful variables for setting these:

```ruby
    File {
      owner => $puppet_enterprise::params::root_user,
      group => $puppet_enterprise::params::root_group,
      mode  => $puppet_enterprise::params::root_mode,
    }
```

Next, put all relevant plugin files into place, using the `$plugin_basedir` variable we set above:

```ruby
file {"${plugin_basedir}/agent/nrpe.ddl":
  ensure => file,
  source => 'puppet:///modules/mco_plugins/mcollective-nrpe-agent/agent/nrpe.ddl',
}

file {"${plugin_basedir}/agent/nrpe.rb":
  ensure => file,
  source => 'puppet:///modules/mco_plugins/mcollective-nrpe-agent/agent/nrpe.rb',
}
```

### \(Optional\) Step 4: Configure the plugin

Some agent plugins require extra configuration to work properly. If present, these settings must be present on every **agent node** that will be using the plugin.

The main `server.cfg` file is managed by the `mcollective` class in the `puppet_enterprise` module. Although editing it is possible, it is *not supported.* Instead, you should take advantage of the MCollective daemon's plugin config directory, which is located at `"${mco_etc}/plugin.d"`.

-   File names in this directory should be of the format `<agent name>.cfg`.
-   Setting names in plugin config files are slightly different:

    |In server.cfg|In $\{mco\_etc\}/plugin.d/nrpe.conf|
    |-------------|-----------------------------------|
    |`plugin.nrpe.conf_dir = /etc/nagios/nrpe`|`conf_dir = /etc/nagios/nrpe`|

    You can use a normal file resource to create these config files with the appropriate values. For simple configs, you can set the content directly in the manifest; for complex ones, you can use a template.

    ```ruby
    file {"${mco_etc}/plugin.d/nrpe.cfg":
      ensure  => file,
      content => "conf_dir = /etc/nagios/nrpe\n",
    }
    ```


### Distributing Policy files

You can also distribute policy files for the ActionPolicy authorization plugin. This can be a useful way to completely disable certain unused actions, limit actions so they can only be used on a subset of your agent nodes, or allow certain actions from the command line.

These files should be named for the agent plugin they apply to, and should go in `${mco_etc}/policies/<plugin name>.cfg`. Policy files should be distributed to every agent node that runs the plugin you are configuring.

**Note:** The `policies` directory doesn't exist by default; you will need to use a `file` resource with `ensure => directory` to initialize it.

[The policy file format is documented here.](https://github.com/puppetlabs/mcollective-actionpolicy-auth#readme) When configuring caller IDs in policy files, note that PE uses the following ID by default:

`cert=peadmin-public` — the command line client, as used by the `peadmin` user on the Puppet master server.

**Example:** This code would completely disable the package plugin's `update` option, to force users to do package upgrades through your centralized Puppet code:

```ruby
file {"${mco_etc}/policies": ensure => directory,}

file {"${mco_etc}/policies/package.policy":
  ensure  => file,
  content => "policy default allow
deny	*	update	*	*
",
}
```

**Note:** Additionally, you can use the `puppet_enterprise::profile::mcollective::agent::allowed_actions` parameter to create policy files. Use `allowed_actions` to specify agent plugins you want to apply an action policy to, and a list of the actions you want to explicitly allow. If a plugin is not specified, no policy is created for it. The behavior without a policy is determined by the `allow_no_actionpolicy` parameter. We've also included a default policy for the `package` plugin that disables `install`, `uninstall`, and `purge`.

### Step 5: Assign the class to nodes

For plugins you are distributing to **all agent nodes,** you can use the console to assign your class to the special `PE MCollective` group. \(This group is automatically maintained by the console, and contains all nodes which have not been added to the special `no mcollective` group.\)

For plugins you are only distributing to **some** agent nodes, you must do the following:

-   Create two Puppet classes for the plugin: a main class that installs everything, and a "client" class that only installs the `.ddl` file and the supporting plugins.
-   Assign the main class to any agent nodes that should be running the plugin.
-   Assign the "client" class to the `PE Console` and `PE Master` groups in the console. \(These special groups contain all of the console and Puppet master nodes in your deployment, respectively.\)

### Step 6: Run Puppetand confirm the plugin is installed

You can either wait for the next scheduled run, or trigger an on-demand run .

Follow the instructions in the [MCollective documentation](https://docs.puppet.com/mcollective/deploy/plugins.html#verifying-installed-agent-plugins) to verify that your new plugins are properly installed.

## Example plugin class

This is an example of a class that installs the `nrpe` plugin.

The `files` directory of the module would simply contain a complete copy of [the nrpe plugin’s Git repo](https://github.com/puppetlabs/mcollective-nrpe-agent). In this example, we are not creating separate agent and client classes.

```
# /etc/puppetlabs/code/environments/production/modules/mco_plugins/manifests/nrpe.pp
class mco_plugins::nrpe {
  Class['puppet_enterprise::mcollective::server::plugins'] -> Class[$title] ~> Service['mcollective']
  include puppet_enterprise::params
  $plugin_basedir = "${puppet_enterprise::params::mco_plugin_userdir}/mcollective"
  $mco_etc        = $puppet_enterprise::params::mco_etc

  File {
    owner => $puppet_enterprise::params::root_user,
    group => $puppet_enterprise::params::root_group,
    mode  => $puppet_enterprise::params::root_mode,
  }

  file {"${plugin_basedir}/agent/nrpe.ddl":
    ensure => file,
    source => 'puppet:///modules/mco_plugins/mcollective-nrpe-agent/agent/nrpe.ddl',
  }

  file {"${plugin_basedir}/agent/nrpe.rb":
    ensure => file,
    source => 'puppet:///modules/mco_plugins/mcollective-nrpe-agent/agent/nrpe.rb',
  }

  file {"${plugin_basedir}/aggregate/nagios_states.rb":
    ensure => file,
    source => 'puppet:///modules/mco_plugins/mcollective-nrpe-agent/aggregate/nagios_states.rb',
  }

  file {"${plugin_basedir}/application/nrpe.rb":
    ensure => file,
    source => 'puppet:///modules/mco_plugins/mcollective-nrpe-agent/application/nrpe.rb',
  }

  file {"${plugin_basedir}/data/nrpe_data.ddl":
    ensure => file,
    source => 'puppet:///modules/mco_plugins/mcollective-nrpe-agent/data/nrpe_data.ddl',
  }

  file {"${plugin_basedir}/data/nrpe_data.rb":
    ensure => file,
    source => 'puppet:///modules/mco_plugins/mcollective-nrpe-agent/data/nrpe_data.rb',
  }

  # Set config: If this setting were in the usual server.cfg file, its name would
  # be plugin.nrpe.conf_dir
  file {"${mco_etc}/plugin.d/nrpe.cfg":
    ensure  => file,
    content => "conf_dir = /etc/nagios/nrpe\n",
  }

}

```

