---
layout: default
title: "Application manifest: Timeouts"
--- 

## PreInstallTimeout

<table>
<tr><td>Applies to:</td><td>PreInstall</td></tr>
<tr><td>Default:</td><td>900 seconds</td></tr>
</table>

The time, in seconds, that Pipelines for Applications waits for the `PreInstall` manifest section to complete.

~~~
username/AppName:
  PreInstallTimeout: 1000
~~~

## InstallTimeout

<table>
<tr><td>Applies to:</td><td>Install</td></tr>
<tr><td>Default:</td><td>900 seconds</td></tr>
</table>

The time, in seconds, that Pipelines for Applications waits for the `Install` manifest section to complete.

~~~
username/AppName:
  InstallTimeout: 1000
~~~

## PostInstallTimeout

<table>
<tr><td>Applies to:</td><td>PostInstall</td></tr>
<tr><td>Default:</td><td>900 seconds</td></tr>
</table>

The time, in seconds, that Pipelines for Applications waits for the `PostInstall` manifest section to complete.

~~~
username/AppName:
  PostInstallTimeout: 1000
~~~

## PreTerminateTimeout

<table>
<tr><td>Applies to:</td><td>PreTerminate</td></tr>
<tr><td>Default:</td><td>900 seconds</td></tr>
</table>

The time, in seconds, that Pipelines for Applications waits for the `PreTerminate` manifest section to complete.

~~~
username/AppName:
  PreTerminateTimeout: 1000
~~~

## TerminateTimeout

<table>
<tr><td>Applies to:</td><td>Terminate</td></tr>
<tr><td>Default:</td><td>900 seconds</td></tr>
</table>

The time, in seconds, that Pipelines for Applications waits for the `Terminate` manifest section to complete.

~~~
username/AppName:
  TerminateTimeout: 1000
~~~

## PostTerminateTimeout

<table>
<tr><td>Applies to:</td><td>PostTerminate</td></tr>
<tr><td>Default:</td><td>900 seconds</td></tr>
</table>

The time, in seconds, that Pipelines for Applications waits for the `PostTerminate` manifest section to complete.

~~~
username/AppName:
  PostTerminateTimeout: 1000
~~~

## PreRestartTimeout

<table>
<tr><td>Applies to:</td><td>PreRestart</td></tr>
<tr><td>Default:</td><td>900 seconds</td></tr>
</table>

The time, in seconds, that Pipelines for Applications waits for the `PreRestart` manifest section to complete.

~~~
username/AppName:
  PreRestartTimeout: 1000
~~~

## PostRestartTimeout

<table>
<tr><td>Applies to:</td><td>PostRestart</td></tr>
<tr><td>Default:</td><td>900 seconds</td></tr>
</table>

The time, in seconds, that Pipelines for Applications waits for the `PostRestart` manifest section to complete.

~~~
username/AppName:
  PostRestartTimeout: 1000
~~~

## PreStartTimeout

<table>
<tr><td>Applies to:</td><td>(./manifest.html#manifest-sections)PreStart</td></tr>
<tr><td>Default:</td><td>900 seconds</td></tr>
</table>

The time, in seconds, that Pipelines for Applications waits for the `PreStart` manifest section to complete.

~~~
username/AppName:
  PreStartTimeout: 1000
~~~

## StartTimeout

<table>
<tr><td>Applies to:</td><td>(./manifest.html#manifest-sections)Start</td></tr>
<tr><td>Default:</td><td>900 seconds</td></tr>
</table>

The time, in seconds, that Pipelines for Applications waits for the `Start` manifest section to complete.

~~~
username/AppName:
  StartTimeout: 1000
~~~

## PostStartTimeout

<table>
<tr><td>Applies to:</td><td>(./manifest.html#manifest-sections)PostStart</td></tr>
<tr><td>Default:</td><td>900 seconds</td></tr>
</table>

The time, in seconds, that Pipelines for Applications waits for the `PostStart` manifest section to complete.

~~~
username/AppName:
  PostStartTimeout: 1000
~~~

## StartSuccessTime

<table>
<tr><td>Applies to:</td><td>(./manifest.html#manifest-sections)Exec</td></tr>
<tr><td>Default:</td><td>5 seconds</td></tr>
</table>

The time, in seconds, that Pipelines for Applications waits for the `Exec` process to begin.

~~~
username/AppName:
  StartSuccessTime: 10
~~~

## StopTimeout

<table>
<tr><td>Applies to:</td><td>(./manifest.html#manifest-sections)Exec</td></tr>
<tr><td>Default:</td><td>10 seconds</td></tr>
</table>

The time, in seconds, that Pipelines for Applications waits for the `Exec` process to gracefully stop.

~~~
username/AppName:
  StopTimeout: 20
~~~

## RestartDelay

<table>
<tr><td>Applies to:</td><td>(./manifest.html#manifest-sections)Exec</td></tr>
<tr><td>Default:</td><td>1 seconds</td></tr>
</table>

The time, in seconds, that Pipelines for Applications waits between spawning multiple `Exec` processes. Also see `ProcessCount`.

~~~
username/AppName:
  RestartDelay: 2
~~~
