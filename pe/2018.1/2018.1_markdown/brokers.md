---
author: melissa amos <melissa.amos@puppet.com\>
---

# Brokers

Brokers hand off nodes to configuration management systems like Puppet Enterprise. Brokers consist of two parts: a broker type and information specific to the broker type.

Razor ships with three default broker types:

-   `puppet-pe` — Hands off node management to Puppet Enterprise. This broker specifies the address of the Puppet server, the Puppet Enterprise version, and for Windows, the location of the Windows agent installer.
-   `puppet` — Hands off management to open source Puppet. This broker specifies the address of the Puppet server, the node certname, and the environment.
-   `noop` — Doesn't hand off management. A no-op broker can be useful for getting started quickly or doing a basic installation without configuration management.

You can create brokers using the `create-broker` command. You can also update configuration details for the broker and delete brokers.

Writing new broker types is only necessary to use Razor with other configuration management systems.

## Broker storage directories

There are two directories that store brokers:

-   `/opt/puppetlabs/server/apps/razor-server/share/razor-server/brokers` stores default brokers shipped with the product.
-   `/etc/puppetlabs/razor-server/brokers` stores custom brokers that you create.

**Tip:** We recommend not modifying the directory or brokers at `/opt...`, but you can copy brokers from there to the custom broker directory and modify them as needed.

## Creating a PE broker

To create a PE broker that enrolls nodes with the master at `puppet-master.example.com`:

```
 razor create-broker --name=my_puppet --broker-type=puppet-pe \
    --configuration server=puppet.example.org \
    --configuration version=2015.3
```

## Writing the broker install script

The broker install script is generated from the `install.erb` \(\*nix\) or `install.ps1.erb` \(Windows\) template of your broker.

The template returns a valid shell script because tasks generally perform the handoff to the broker by running a command like `curl -s <%= broker_install_url %> | /bin/bash`. The `GET` request to `broker_install_url` \(\*nix\) or `broker_install_url('install.ps1')` \(Windows\) returns the broker’s install script after interpolating the template.

In the install template, you have access to two objects: `node` and `broker`.

The `node` object gives you access to node facts \(`node.facts["example"]`\), tags \(`node.tags`\), and metadata \(`node.metadata['key']`\).

The `broker` object gives you access to the configuration settings. For example, if your `configuration.yaml` specifies that a setting `version` must be provided when creating a broker from this broker type, you can access the value of `version` for the current broker as `broker.version`.

## Writing the broker configuration file

The `configuration.yaml` file indicates what parameters can be supplied for any given broker type.

For each parameter, you can supply these attributes:

-   `description` — Human-readable description of the parameter.

-   `required` — `true` to indicate that the parameter must be supplied. Parameters that aren’t required are optional.

-   `default` — Value for the parameter if one isn’t supplied.

As an example, here's the configuration.yaml for the PE broker type:

```
server:
    description: "The puppet master to load configurations and installation packages from."
version:
    description: "Override the PE version to install; defaults to `current`."
windows_agent_download_url:
    description: "The download URL for a Windows PE agent installer; defaults to a URL derived from the `version` config."
```

## Create a new broker type

To create a broker called `sample`:

### About this task

### Procedure

1.  From the command line, create a `sample.broker` directory anywhere on the `broker_path`.

    The `broker_path` is specified in the `broker_path` class parameter of the `pe_razor` class. By default, the broker directory for custom brokers is `/etc/puppetlabs/razor-server/brokers`.

2.  Write the broker install script and place it in the `install.erb` \(\*nix\) or `install.ps1.erb` \(Windows PowerShell\) template in the `sample.broker` directory.

3.  If the broker type requires configuration data, write the broker configuration file and save it in the `sample.broker` directory.


### Results

**Related information**  


[\(Optional\) Create a broker](provisioning_a_windows_node.md#)

[Broker commands](using_the_razor_client.md#)

[Brokers API](using_the_razor_api.md#)

