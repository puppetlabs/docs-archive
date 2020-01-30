---
layout: default
title: "Agent commands"
---

## /usr/local/bin/distelli agent dump

Prints the agent processes, deployed applications, and current deployments.

<h4>SYNTAX</h4>

~~~
/usr/local/bin/distelli agent dump
~~~

<h4>OPTIONS</h4>

none

<h4>USAGE</h4>

This command is typically used for troubleshooting or to see what applications are deployed and running on the server.

<h4>EXAMPLES</h4>

~~~
/usr/local/bin/distelli agent dump
Processes:
DistelliAgent Running 1136 2015-07-08 13:14:01 PDT (3 hours ago)
envs/SA_Linux Running 13200 2015-07-08 16:40:11 PDT (2 minutes ago)

Apps:
d123:simpleapp:sa_linux Running /distelli/envs/SA_Linux/bin/distelli-run.sh Daemon
d-a123456789b123456789
a-c987654321d987654321
-1/10/900/900/900
~~~

## /usr/local/bin/distelli agent help

Prints the agent commands and brief help message for each.

<h4>SYNTAX</h4>

~~~
/usr/local/bin/distelli agent help
~~~

<h4>OPTIONS</h4>

None.

<h4>USAGE</h4>

Use to get help.

<h4>EXAMPLE</h4>

~~~
/usr/local/bin/distelli agent help
Usage: /usr/local/bin/distelli agent -dtk-dir <dir> <command>

Connect your server to Pipelines

-dtk-dir <dir>
Undocumented, defaults to /distelli

stop
Stops all processes being supervised, and stops the supervisor, use with CAUTION because all applications deployed (and the agent) will be stopped

version
Display the version of the agent

status
Check if the agent is running and print the server name

uninstall
Uninstall the Pipelines agent permanently.

run
Run the Pipelines agent in the foreground, printing log files to STDERR. This command should be run only as a child of `dtk supervise`.

install
Install and possibly start (or restart) the agent.

dump
Print the processes, applications, and current deployments

loglevel debug|info|warn|error
Set the log level

start
Start (or restart) the agent.

supervise
Undocumented
~~~

## /usr/local/bin/distelli agent install

Install and possibly start (or restart) the agent.

<h4>SYNTAX</h4>

~~~
/usr/local/bin/distelli agent install [-nostart] [-onboot] [-conf FILE]
~~~


<h4>OPTIONS</h4>

~~~
-nostart
~~~

This will install the agent, but not start it. Manual action will be required to start the agent.

~~~
-onboot
~~~

This will install the agent, but not start it. The agent will automatically start after the next reboot.

~~~
-conf FILE
~~~


This option tells the agent to read a distelli.yml for the agent access token and secret key. See [distelli.yml usage](./distelliyml.html) for more information.

<h4>USAGE</h4>


The `/usr/local/bin/distelli agent install` command can be used to change the agent behavior. Using this command you can specify install options, as noted above.
If you issue this command on a server that has running deployed software, it will continue to run. If you specify the -nostart option, the applications will not continue to run after a boot of the server. Doing a `/usr/local/bin/distelli agent install` or `/usr/local/bin/distelli agent start` will (re)start the deployed applications.

> **Caution:** The above comments assume the application running was started with the [Exec section of the distelli-manifest.yml](./manifest.html).


<h4>EXAMPLES</h4>

Reinstall the agent and tell it not to start until next boot.

~~~
/usr/local/bin/distelli agent install -onboot
~~~

## /usr/local/bin/distelli agent loglevel

Set the agent logging level. Logging is done to /distelli/logs/agent.log. The default is **info**.

> **Caution:** This command is typically used only under the guidance of Puppet Pipelines support.

<h4>SYNTAX</h4>

~~~
/usr/local/bin/distelli agent loglevel debug|error|info|warn
~~~

<h4>OPTIONS</h4>


~~~
debug
~~~

Sets the logging level to debug. The highest level of logging.

~~~
error
~~~

Sets the logging level to error. The agent will only log errors.

~~~
info
~~~

Sets the logging level to info. The agent will log information messages that highlight progress. This is the default level.

~~~
warn
~~~

Sets the logging level to warn. Only potentially harmful situations will be logged.

<h4>USAGE</h4>

This command will typically be used only when requested by Puppet Pipelines support.

<h4>EXAMPLES</h4>

Set the logging level to **error**.

~~~
/usr/local/bin/distelli agent loglevel error
~~~

## /usr/local/bin/distelli agent run

This is an internal command used to run the agent.

> **Caution:** This command is typically used only under the guidance of Puppet Pipelines support.

<h4>SYNTAX</h4>

~~~
/usr/local/bin/distelli agent run
~~~

<h4>OPTIONS</h4>

None.

<h4>USAGE</h4>

This command is used by internal mechanisms to run the agent.

<h4>EXAMPLES</h4>

None.

## /usr/local/bin/distelli agent start

This will start the agent. if the agent is already running, this will restart the agent.

<h4>SYNTAX</h4>

~~~
/usr/local/bin/distelli agent start
~~~

<h4>OPTIONS</h4>

None.

<h4>USAGE</h4>

Used to start the agent. If the agent was installed with the `-nostart` option, this command `/usr/local/bin/distelli agent start` will start the agent.

<h4>EXAMPLES</h4>

Starting the agent.

~~~
/usr/local/bin/distelli agent start
Running under 'jdoe' account
Starting upstart daemon with name:\tdtk-supervise-cc123345677ad94a8d34ac610381242f9ae28bb8
~~~

## /usr/local/bin/distelli agent status

Shows the current status of the agent.

<h4>SYNTAX</h4>

~~~
/usr/local/bin/distelli agent status
~~~

<h4>OPTIONS</h4>

None.

<h4>USAGE</h4>

Use this command to check the status of the agent. This is useful when confirming that the agent was properly installed.

<h4>EXAMPLES</h4>

Look at the status of a running agent.

~~~
/usr/local/bin/distelli agent status
Distelli Agent (serverA) is Running with id 766b88c8-e925-11e4-ae8b-080027cc07f7
~~~

Look at the status of a stopped agent.

~~~
/usr/local/bin/distelli agent status
Distelli Agent is Stopped
~~~

## /usr/local/bin/distelli agent stop

Stops the running agent.

<h4>SYNTAX</h4>

~~~
/usr/local/bin/distelli agent stop
~~~

<h4>OPTIONS</h4>

None.

<h4>USAGE</h4>

Use this to stop the agent running in the background.

<h4>EXAMPLES</h4>

Stop the agent.

~~~
/usr/local/bin/distelli agent stop
~~~

## /usr/local/bin/distelli agent uninstall

Use this to uninstall the agent.

<h4>SYNTAX</h4>

~~~
/usr/local/bin/distelli agent uninstall
~~~

<h4>OPTIONS</h4>

~~~
--yes
~~~

The `--yes` option facilitates a quiet deletion and auto-answers `yes` to the **Continue? (yes/no)** prompt.

<h4>USAGE</h4>

Uninstalling the agent will cause the agent to stop running and for the agent to destroy its Unique ID (UID).
<ul>
<li>All Pipelines deployed applications will be removed from the server.</li>

<li>The server will be removed from all environments.</li>

<li>The server will be removed from its Pipelines account.</li>

<li>The agent supervise process will no longer start at boot.</li>

</ul>

Uninstalling the agent doesn't remove the agent files, which allows for a future [/usr/local/bin/distelli agent install](./agent.html).

<h4>EXAMPLES</h4>


~~~
/usr/local/bin/distelli agent uninstall
Are you sure you want to uninstall the Distelli Agent? (to avoid this prompt, pass --yes flag)
 * All applications will be removed from this server
 * The server will be removed from all environments
 * The server will be removed from the web interface
 * The supervise process will be removed from 'upstart'
Continue? (yes/no)
yes
#
~~~

## /usr/local/bin/distelli agent version

Shows the current version of the installed agent.

<h4>SYNTAX</h4>

~~~
/usr/local/bin/distelli agent version
~~~

<h4>OPTIONS</h4>

None.

<h4>USAGE</h4>

Show the version of the Pipelines agent.

<h4>EXAMPLES</h4>

~~~
/usr/local/bin/distelli agent version
Distelli Agent 3.34
Copyright (C) 2015 Distelli Inc.
~~~

