---
layout: default
title: "Application manifest: Restart section"
--- 

The **Restart** section of the Pipelines for Applications application manifest contains instructions about how to restart your application. A restart will stop the existing application process that was started with an `Exec` section, then attempt to start the process again.

The manifest is read and processed in a specific order: 

1. Env
1. PreRestart
1. PreStart 
1. Start
1. Exec
1. PostStart 
1. PostRestart 

## Env 

<table>
<tr>
<td><b>Manifest Section</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>After</b></td>
<td><b>Before</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>Build</b></td>
<td><b>Deploy</b></td>
<td><b>Restart</b></td>
<td><b>Terminate</b></td>
</tr>
<tr>
<td>Env</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>NA</td>
<td>PreInstall</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td>X</td>
<td> </td>
<td> </td>
</tr>
<tr>
<td></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>NA</td>
<td>PreRestart</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td> </td>
<td>X</td>
<td> </td>
</tr>
<tr>
<td></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>NA</td>
<td>PreTerminate</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td> </td>
<td> </td>
<td>X</td>
</tr>
</table>

This is an ordered map of key-value pairs that specify any environment variables that must be set before the application is started. These environment variables are available to the commands in all the sections of the deploy, restart, and terminate manifest.

These variables are not available during builds.

~~~
username/AppName:
  Env:
    - PORT: "8080"
    - DATE: '`date -u`'
    - RVM_HOME: '/usr/local/rvm'
    - SOME_VAR: '`cat /path/to/some/file`'
~~~

## PreRestart

<table>
<tr>
<td><b>Manifest Section</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>After</b></td>
<td><b>Before</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>Build</b></td>
<td><b>Deploy</b></td>
<td><b>Restart</b></td>
<td><b>Terminate</b></td>
</tr>
<tr>
<td>PreRestart</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>Env</td>
<td>PreStart</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td> </td>
<td>X</td>
<td> </td>
</tr>
</table>

This is a list of commands to run during the `PreRestart` step of the deployment. If this section is omitted, the `PreRestart` step is skipped.

~~~
username/AppName:
  PreRestart:
    - echo "Running PreRestart step"
    - remove-from-loadbalancer.sh
    - sleep 10
~~~

## PreStart 

<table>
<tr>
<td><b>Manifest Section</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>After</b></td>
<td><b>Before</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>Build</b></td>
<td><b>Deploy</b></td>
<td><b>Restart</b></td>
<td><b>Terminate</b></td>
</tr>
<tr>
<td>PreStart</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>*Flip</td>
<td>Start</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td>X</td>
<td> </td>
<td> </td>
</tr>
<tr>
<td></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td>Exec</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td>X</td>
<td> </td>
<td> </td>
</tr>
<tr>
<td></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>PreRestart</td>
<td>Start</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td> </td>
<td>X</td>
<td> </td>
</tr>
<tr>
<td></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td>Exec</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td> </td>
<td>X</td>
<td> </td>
</tr>
</table>

This is a list of commands to run during the `PreStart` step of the deployment. If this section is omitted, the `PreStart` step is skipped.

~~~
username/AppName:
  PreStart:
    - echo "Running PreStart step"
    - remove-from-loadbalancer.sh
    - sleep 10
~~~

## Start 

> **Caution:** You must not use the `Start` and `Exec` sections of the manifest at the same time. `Exec` takes precedence if both are used.

<table>
<tr>
<td><b>Manifest Section</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>After</b></td>
<td><b>Before</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>Build</b></td>
<td><b>Deploy</b></td>
<td><b>Restart</b></td>
<td><b>Terminate</b></td>
</tr>
<tr>
<td>Start</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>PreStart</td>
<td>PostStart</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td>X</td>
<td>X</td>
<td> </td>
</tr>
</table>

This is a list of commands to run during the `Start` step of the deployment. If this section is omitted, the `Start` step is skipped.

The `Start` section should be used for starting long-lived background processes. Any process started in this section will not be monitored by the Pipelines agent.

Use the `Start` section for things like:

* Starting NGINX or Apache.

~~~
username/appname:
  Start:
    - sudo service nginx start
~~~

* Starting a Node.js application with `forever`.

~~~
username/appname:
  Start:
    - forever start app.js
~~~

* Starting a database daemon.

~~~
username/appname:
  Start:
    - bin/monogodb fork
~~~

Daemons and background processes can be started, restarted, and stopped in other sections of the manifest besides the `Start` section, such as `PreInstall`, `PostInstall`, `PreStart`, and `Terminate`.

## Exec 

> **Caution:** You must not use the `Start` and `Exec` sections of the manifest at the same time. `Exec` takes precedence if both are used.

<table>
<tr>
<td><b>Manifest Section</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>After</b></td>
<td><b>Before</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>Build</b></td>
<td><b>Deploy</b></td>
<td><b>Restart</b></td>
<td><b>Terminate</b></td>
</tr>
<tr>
<td>Exec</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>PreStart</td>
<td>PostStart</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td>X</td>
<td>X</td>
<td> </td>
</tr>
</table>

This is a command to run during the `Exec` step of the deployment. If this section is omitted, the `Exec` step is skipped.

The `Exec` section of the manifest is used for starting a foreground process that will be distinctly monitored by the Pipelines agent. After deploy, if the process started by the `Exec` section is stopped by anything other than the Pipelines agent, the agent will attempt to restart the process by re-executing the `Exec` section's command(s).

This section should not be used to start a background process or daemon. The process started by the `Exec` section should not fork itself into the background, but rather should actively run. If the process started in the `Exec` section forks into the background and the agent cannot actively monitor it, the deploy will fail.

Use the `Exec` section for things like:

* Starting a Node.js application.

~~~
username/appname:
  Exec:
    - node app.js
~~~

* Starting a Java application.

~~~
username/appname:
  Env:
    - CLASSPATH: 'target/*:lib/*'
    - JAVA_HOME: "/usr/lib/jvm/default-java/jre"
    - JVM_ARGS: '"-Duser.timezone=UTC -Xmx128M -Xms128M"'
    - PORT: "8001"
    - ARGS: "$PORT"
  Exec:
    - $JAVA_HOME/bin/java -cp $CLASSPATH $JVM_ARGS com.example.app.SimpleJavaApp $ARGS
~~~

* Running a C# application.

~~~
username/appname:
  Exec:
    - release\webserver.exe
~~~

The foreground process of your application should be executed only in the `Exec` section of the manifest.

## PostStart 

<table>
<tr>
<td><b>Manifest Section</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>After</b></td>
<td><b>Before</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>Build</b></td>
<td><b>Deploy</b></td>
<td><b>Restart</b></td>
<td><b>Terminate</b></td>
</tr>
<tr>
<td>PostStart</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>Start</td>
<td>NA</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td>X</td>
<td> </td>
<td> </td>
</tr>
<tr>
<td></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>Exec</td>
<td> </td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td>X</td>
<td> </td>
<td> </td>
</tr>
<tr>
<td> </td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>Start</td>
<td>PostRestart</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td> </td>
<td>X</td>
<td> </td>
</tr>
<tr>
<td></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>Exec</td>
<td> </td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td> </td>
<td>X</td>
<td> </td>
</tr></table>

This is a list of commands to run during the `PostStart` step of the deployment. If this section is omitted, the `PostStart` step is skipped.

~~~
username/AppName:
  PostStart:
    - echo "Running PostStart step"
    - add-to-loadbalancer.sh
    - sleep 10
~~~

## PostRestart

<table>
<tr>
<td><b>Manifest Section</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>After</b></td>
<td><b>Before</b></td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td><b>Build</b></td>
<td><b>Deploy</b></td>
<td><b>Restart</b></td>
<td><b>Terminate</b></td>
</tr>
<tr>
<td>PostRestart</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>PostStart</td>
<td>NA</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td> </td>
<td>X</td>
<td> </td>
</tr>
</table>

This is a list of commands to run during the PostRestart step of the deployment. If this section is omitted, the PostRestart step is skipped.

~~~
username/AppName:
  PostRestart:
    - echo "Running PreRestart step"
    - add-to-loadbalancer.sh
    - sleep 10
~~~
