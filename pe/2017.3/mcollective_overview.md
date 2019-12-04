# MCollective

With MCollective in Puppet Enterprise, you can invoke many kinds of actions in parallel across any number of nodes. Several useful actions are available by default, and you can easily add and use new actions.

## Actions and plugins

MCollective tasks aren't quite like SSH, PowerShell, or other tools meant for running arbitrary shell code in an ad-hoc way.

MCollective is built around the idea of predefined **actions**—it is essentially a highly parallel **remote procedure call \(RPC\)** system.

**Actions** are distributed in **agent plugins**, which are bundles of several related actions.

-   Many plugins are available by default; see Built-in actions.
-   You can download or write new plugins and adding them to the engine.

### Invoking actions and filtering nodes

The core concept of MCollective engine is **invoking actions**, in parallel, on a select group of nodes.

Typically you choose some nodes to operate on \(usually with a **filter** that describes the desired fact values or classes\), and specify an **action** and its **arguments**. MCollective then runs that action on the chosen nodes, and displays any data collected during the run.

You can invoke MCollective actions from the command line

In addition to the main action invocation interfaces, PE provides special interfaces for one of the most useful orchestration tasks, remotely controlling the agent and triggering runs.

## MCollective orchestration internals

The following explain the internals of MCollective orchestration.

### Components

MCollective consists of the following parts:

-   The `pe-activemq` service \(which runs on the Puppet master server\) routes all messages.
-   The `pe-mcollective` service \(which runs on every agent node\) listens for authorized commands and invokes actions in response. It relies on the available agent plugins for its set of possible actions.
-   The `mco` command \(available to the `peadmin` user account on the Puppet master server\) can issue authorized commands to any number of nodes.

### Security

The MCollective engine uses the same security model as the recommended "standard MCollective deployment." See the security model section for a more detailed rundown of these security measures.

In short, all commands and replies are encrypted in transit, and only a few authorized clients are permitted to send commands. By default, PE allows commands to be sent by:

-   Read/write and admin users of the console
-   Users able to log in to the Puppet master server with full administrator `sudo` privileges

If you extend MCollective by integrating external applications, you can limit the actions each application has access to by distributing policy files; see the configuring orchestration page for more details.

You can also allow additional users to log in as the `peadmin` user on the Puppet master, usually by distributing standard SSH public keys.

### Network traffic

Every node \(including all agent nodes, the Puppet master server, and the console\) needs the ability to initiate connections to the Puppet master server over TCP port 61613. See the notes on firewall configuration for more details about PE's network traffic.

