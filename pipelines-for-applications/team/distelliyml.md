---
layout: default
title: "Using the distelli.yml file"
---

The `distelli.yml` file provides authentication credentials for automated use of the Pipelines agent and the Pipelines CLI.

<h2><a name="using-distelli.yml-with-distell-agent"></a>Using distelli.yml with Pipelines agent</h2>

The distelli.yml file allows automated installation of the [Pipelines agent](./agent.html). To use this feature:
<ol>
<li>Create the file in the appropriate location.</li>
<li>Add the correct credentials.</li>
<li>Install the Pipelines agent.</li>
</ol>

When the agent installs, it will use the specified credentials to add the server to the specified Puppet Pipelines account. You can also specify options to automate adding the server to specific environments.

<h4>Linux</h4>

To use the distelli.yml for automating authentication and installation features of the Pipelines agent, the distelli.yml may be placed in the /etc directory.

~~~
/etc/distelli.yml
~~~

<h4>Windows</h4>

In a Windows environment, the distelli.yml may be placed in the %SystemDrive%.

~~~
%SystemDrive%\distelli.yml
~~~

<h4><a name="login-the-distelli-agent"></a>Log into the Pipelines agent</h4>

To use the distelli.yml for authenticating the Pipelines agent, use this syntax:

~~~
distelli agent install -conf /etc/distelli.yml
~~~

<h2><a name="using-distelli.yml-with-distelli-cli"></a>Using distelli.yml with the Pipelines CLI</h2>

To use the distelli.yml for automating authentication with the Pipelines CLI, the distelli.yml file may be placed in the user's home directory.

~~~
~/distelli.yml
~~~

To view an example of this, see [Auto-deploy from your CI platform](./deployment-ci.html).

<h4><a name="login-the-distelli-cli"></a>Log into the Pipelines CLI</h4>

To use the distelli.yml for authenticating the Pipelines CLI, use this syntax:

~~~
distelli login -conf ~/distelli.yml
~~~

## Formatting the distelli.yml file

Here is an example file. Access token and secret key are required. All other fields are optional. 

~~~
  DistelliAccessToken: '12345678901234567890123456'
  DistelliSecretKey: '1234567890123456789012345678901234567'
  Environments:
    - ENVIRONMENT1
    - ENVIRONMENT2
  BuildCapabilities:
    - Docker
    - SFOIntranet
  IsBuildServer: true
~~~

<h3><a name="specifying-credentials"></a>Specifying credentials</h3>

**Required.** When using the distelli.yml file, the following fields are required:

~~~
DistelliAccessToken:
DistelliSecretKey:
~~~

**DistelliAccessToken:** This is the access token associated with a specific Puppet Pipelines account. Provide this, and the secret key, to have the Pipelines agent automatically connect the server to a Puppet Pipelines account on agent installation.

**DistelliSecretKey:** This is the secret key associated with a specific Pipelines account. Provide this to have the Pipelines agent automatically connect the server to a Puppet Pipelines account on agent installation.

These fields are tied to a Puppet Pipelines account. Ensure you are [using the correct account](./users.html) when retrieving these values.

<h4>Retrieving credentials</h4>

<ol>
<li>Click the <b>gear</b> icon from the top right.</li>
<li>Click the <b>Agent</b> icon on the left.</li>
<li>Under <i>Secret Key</i> click the <b>Show</b> button.</li>
</ol>

Here you will find the Access Token and Secret Key. Add them to the distelli.yml. For example:

~~~
DistelliAccessToken: '12345678901234567890123456'
DistelliSecretKey: '1234567890123456789012345678901234567'
~~~

With this file in place, when you do a Pipelines agent install, you will not be prompted for manual entry of your email credentials. Instead the agent will use the credentials in the distelli.yml file to connect the server to a Puppet Pipelines account.

<h3><a name="specifying-environment"></a>Specifying environments</h3>

**Optional.** This section of the file allows the user to specify application environment(s) that the server should be automatically be added to when installing the Pipelines agent.

You can specify one or more application environments for this server to join automatically when installing the Pipelines agent and using a distelli.yml. This assumes the environment(s) specified belong to the Puppet Pipelines account associated with the agent access token and secret key.

This feature is enabled in the <b>Environments:</b> section of the `/etc/distelli.yml` file.

~~~
  Environments:
    - ENVIRONMENT1
    - ENVIRONMENT2
~~~

> **Important:** If the environment setting "Auto Deploy the Active Release when a new Server joins this environment" is enabled (checked) and there is an active release, an auto deploy will occur when servers join an environment using distelli.yml.
    For more information, see [Environment Settings](./environment.html).

### Specifying the capabilities of build servers

**Optional.** In this section of the build server, you can specify any capabilities that should be associated with a server on which you're installing the Pipelines agent.

~~~
  BuildCapabilities:
    - Docker
    - SFOIntranet
~~~ 

**BuildCapabilities** are case-sensitive strings. These capabilities are used in conjunction with your application's [build server capability requirements](build-configure.html#setting-application-build-server-capability-requirements) to schedule builds on a particular subset of servers that have required dependencies. 

### Specifying whether servers are build servers

**Optional.** In this section of the distelli.yml file, you can specify whether the server on which you're installing the Pipelines agent should be marked as a build server. Mark new servers as build servers by setting **IsBuildServer:** to **true**.

~~~
  IsBuildServer: true
~~~

If you set **IsBuildServer** to **false**, the next time the distelli.yml file is read, servers previously marked as build servers will have this designation removed. 


