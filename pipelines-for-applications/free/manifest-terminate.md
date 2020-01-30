---
layout: default
title: "Application manifest: Terminate section"
--- 

The **Terminate** section of the Pipelines for Applications application manifest contains instructions about how to terminate your application. A terminate will stop the existing application process that was started with a manifest's `Exec` section, then remove the application from the server.

The manifest is read and processed in a specific order: 

1. Env
1. PreTerminate
1. Terminate
1. PostTerminate

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

## PreTerminate

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
<td>PreTerminate</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>NA</td>
<td>Terminate</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td> </td>
<td> </td>
<td>X</td>
</tr>
</table>

This is a list of commands to run during the `PreTerminate` step of the deployment. If this section is omitted, the `PreTerminate` step is skipped.

In the `PreTerminate` section you can specify commands to prepare to terminate your application.

~~~
username/AppName:
  PreRestart:
    - echo "Running PreRestart step"
    - remove-from-loadbalancer.sh
    - sleep 10
~~~

## Terminate

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
<td>Terminate</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>PreTerminate</td>
<td>PostTerminate</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td> </td>
<td> </td>
<td>X</td>
</tr>
</table>

This is a list of commands to run during the `Terminate` step of terminate task. If this section is omitted, the `Terminate` step is skipped.

Use the `Terminate` section to issue the commands to terminate your deployed application. It is during this phase that the Pipelines agent will remove your application from the deployed to server.

~~~
username/AppName:
  Terminate:
    - sudo service nginx stop
~~~

## PostTerminate

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
<td>PostTerminate</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td>Terminate</td>
<td>NA</td>
<td style="border-left: 1px solid #cdd0d4;"></td>
<td> </td>
<td> </td>
<td> </td>
<td>X</td>
</tr>
</table>

This is a list of commands to run after a deployed application has been terminated. If this section is omitted, the `PostTerminate` step is skipped.

~~~
username/AppName:
  PostTerminate:
    - echo "Application Terminated"
~~~

