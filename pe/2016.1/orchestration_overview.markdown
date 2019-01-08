---
layout: default
title: "MCollective: Overview"
canonical: "/pe/latest/orchestration_overview.html"
---


Puppet Enterprise includes the MCollective engine, which can invoke many kinds of actions in parallel across any number of nodes. Several useful actions are available by default, and you can easily add and use new actions.

Quick links
-----

**Special tasks:**

- [Controlling Puppet](./orchestration_puppet.html)
- [Browsing and searching resources](./orchestration_resources.html)

**General tasks:**

- [Invoking actions (command line)](./orchestration_invoke_cli.html)
- [List of built-in actions](./orchestration_actions.html)

**Extending MCollective:**

- [Adding new actions](./orchestration_adding_actions.html)

**Configuring MCollective:**

- [Configuring orchestration](./orchestration_config.html)


> **Note:** Sometimes, newly added nodes won't respond immediately to MCollective commands. These nodes will begin responding to commands about 30 minutes after Puppet Enterprise is installed. You can accelerate this by logging into the node and running `puppet agent --test` as an admin user.

MCollective fundamentals
-----

### Actions and plugins

MCollective tasks aren't quite like SSH, PowerShell, or other tools meant for running arbitrary shell code in an ad-hoc way.

PE's MCollective is built around the idea of predefined **actions** --- it is essentially a highly parallel **remote procedure call (RPC)** system.

**Actions** are distributed in **MCollective agent plugins**, which are bundles of several related actions.

* Many plugins are available by default; see [Built-in actions](./orchestration_actions.html).
* You can extend MCollective by downloading or writing new plugins and [adding them to the engine with Puppet](./orchestration_adding_actions.html).

### Invoking actions and filtering nodes

The core concept of PE's MCollective engine is **invoking actions**, in parallel, on a select group of nodes.

Typically you choose some nodes to operate on (usually with a **filter** that describes the desired fact values or Puppet classes), and specify an **action** and its **arguments**. MCollective then runs that action on the chosen nodes, and displays any data collected during the run.

In PE, you can invoke MCollective actions [from the command line](./orchestration_invoke_cli.html)

### Special interfaces: Puppet runs and resources

In addition to the main action invocation interfaces, Puppet Enterprise provides special interfaces for one of the most useful orchestration tasks, [remotely controlling the Puppet agent and triggering Puppet runs](./orchestration_puppet.html).


Orchestration internals
-----

### Components

MCollective consists of the following parts:

- The `pe-activemq` service (which runs on the Puppet master server) routes all MCollective-related messages.
- The `pe-mcollective` service (which runs on every agent node) listens for authorized commands and invokes actions in response. It relies on the available agent plugins for its set of possible actions.
- The `mco` command (available to the `peadmin` user account on the Puppet master server) can issue authorized commands to any number of nodes.

### Configuration

See [the Configuring MCollective page][config].

[config]: ./orchestration_config.html

### Security

The MCollective engine in Puppet Enterprise uses the same security model as the recommended "standard MCollective deployment." [See the security model section on the MCollective standard deployment page](/mcollective/deploy/standard.html#security-model) for a more detailed rundown of these security measures.

In short, all commands and replies are encrypted in transit, and only a few authorized clients are permitted to send commands. By default, PE allows commands to be sent by:

- Read/write and admin users of the PE console
- Users able to log in to the Puppet master server with full administrator `sudo` privileges

If you extend MCollective by [integrating external applications][integrate], you can limit the actions each application has access to by distributing policy files; [see the configuring orchestration page][config] for more details.

You can also allow additional users to log in as the `peadmin` user on the Puppet master, usually by distributing standard SSH public keys.

### Network traffic

Every node (including all agent nodes, the Puppet master server, and the console) needs the ability to initiate connections to the Puppet master server over TCP port 61613. See the [notes on firewall configuration in the system requirements chapter of this guide](./install_system_requirements.html#firewall-configuration) for more details about PE's network traffic.


