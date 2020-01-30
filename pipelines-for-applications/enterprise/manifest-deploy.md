---
layout: default
title: "Application manifest: Deploy section"
--- 

The **Deploy** section of the Pipelines for Applications application manifest contains instructions about how to deploy the application release to the servers in the application environment.

The manifest is read and processed in a specific order: 

1. Env
1. PreInstall
1. Install
1. InstallTemplates
1. PostInstall
1. Stop (deployment phase)
1. Flip (deployment phase)
1. PreStart
1. Start
1. Exec 
1. PostStart 

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

## PreInstall 

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
<td>PreInstall</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>Env</td>
<td>Install</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td>X</td>
<td> </td>
<td> </td>
</tr>
</table>

These commands occur before the install phase of a deployment. Often this phase is used to install, set up, and/or configure prerequisites. 

~~~
username/AppName:
  PreInstall:
    - echo "---Begining PreInstall---"
    - echo "--Installing deploy dependencies--"
    - echo "-Updating apt-get-"
    - sudo apt-get -y update
    - echo "-Installing nodejs-"
    - sudo apt-get -y install nodejs
~~~

## Install 

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
<td>Install</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>PreInstall</td>
<td>InstallTemplates</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td>X</td>
<td> </td>
<td> </td>
</tr>
</table>

This is a list of commands to run during the install step of the deployment.

Typically this section is omitted. If it is omitted it is during this phase that Pipelines for Applications unbundles the files specified in the release to the destination server.

~~~
username/AppName:
  Install:
    - sudo apt-get -y update
    - sudo apt-get install -y apache2
    - echo '<html><head><title>Just an example</title></head><body>' > index.html
    - echo '<div id="message"><hr></div><div id="container"><h1>Just a Simple Apache deploy from Distelli!</h1><h1></h1>' >> index.html
    - sudo mv index.html /var/www/html/index.html
~~~

If this `Install` section is included, Pipelines for Applications will not unbundle the files in the release to the destination server, but will instead process the commands in the `Install` section.

## InstallTemplates 

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
<td>InstallTemplates</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>Install</td>
<td>PostInstall</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td>X</td>
<td> </td>
<td> </td>
</tr>
</table>

The list of templates that may have data injected into them from hash files specified in the Pipelines for Applications-specific deploy variable `DISTELLI_INSTALLVIEW`.

~~~
username/AppName:
  Env:
    - DISTELLI_INSTALLVIEW: '"views/Hello.data"'
  InstallTemplates:
    - [ "templates/Hello.template","." ]
  PkgInclude:
    - 'templates/Hello.template'
    - 'views/Hello.data'
~~~

For more information, see [Working with templates](./manifest-template.html).

## PostInstall 

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
<td>PostInstall</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>InstallTemplates</td>
<td>*Stop</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td>X</td>
<td> </td>
<td> </td>
</tr>
</table>

This is a list of commands to run during the `PostInstall` step of the deployment. If this section is omitted, the `PostInstall` step is skipped.

`PostInstall` occurs after the `Install` phase on Deploy tasks. Typically now that the release has been unbundled to the destination server it is this phase where you can manipulate the files.

~~~
username/AppName:
  PostInstall:
    - echo "---Setup nginx to serve php---"
    - sudo rm /etc/nginx/sites-enabled/default
    - sudo cp $DISTELLI_INSTALLHOME/my-default /etc/nginx/sites-available/my-default
    - sudo ln -s /etc/nginx/sites-available/my-default /etc/nginx/sites-enabled/default
    - sudo mv /usr/share/nginx/html/index.html /usr/share/nginx/html/old_index.html
    - sudo mv index.html /var/www/html/index.html
~~~

## Stop

> **Note:** This is not a section of the manifest, but a deployment phase.

<table>
<tr>
<td><b>Deployment Phase</b></td>
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
<td>Stop</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>PostInstall</td>
<td>*Flip</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td>X</td>
<td> </td>
<td> </td>
</tr>
</table>

During the `Stop` phase of deployment, any previously deployed release process specified in the `Exec` section of the manifest will be stopped.

## Flip

> **Note:** This is not a section of the manifest, but a deployment phase.

<table>
<tr>
<td><b>Deployment Phase</b></td>
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
<td>Flip</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>*Stop</td>
<td>PreStart</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td>X</td>
<td> </td>
<td> </td>
</tr>
</table>

During the `Flip` phase of deployment, the symbolic links in `DISTELLI_APPHOME` are flipped to the new deployments in `DISTELLI_INSTALLHOME` automically.

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

## ProcessCount

<table>
<tr><td>Applies to:</td><td>Exec</td></tr>
<tr><td>Default:</td><td>1</td></tr>
</table>

The count of `Exec` processes that will be spawned during a deploy. Also see `RestartDelay`.

~~~
username/AppName:
  ProcessCount: 3
~~~


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


