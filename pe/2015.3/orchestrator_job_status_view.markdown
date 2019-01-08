---
layout: default
title: "Checking and Viewing Puppet Jobs"
canonical: "/pe/latest/orchestrator_job_status_view.html"
---

Use the `puppet job list` command to view a list of running and completed Orchestration jobs.

## Viewing Jobs---Past and Present

To view a list of running and completed jobs, up to 50 maximum (the concurrency limit), ordered by finished timestamp, use the following command: 

~~~
puppet-job list <OPTIONS>
~~~

### Command Options

The following are common options you may use with the `list` action. For a complete list of global options run `puppet job --help`.

Option | Values | Description
-----------|-----------|-----------
`--url`  | flag | Overrides the URL setting for the orchestrator service.
- `--environment`, `--env`, `-e`| environment name | Overrides the environment specified in your orchestrator configuration file.

The command returns something like the following:

~~~
ID  STATUS    TIMESTAMP              USER            ENVIRONMENT  TARGET
----------------------------------------------------------------------------------
234 Running   14:44:03 05-13-15 PST  <Job run user>  production   all
235 Complete  12:12:56 05-12-15 PST  <Job run user>  dev          ExampleApp::FullAppStack
236 Complete  03:33:03 05-03-15 PST  <Job run user>  production   ExampleApp['example\_app_instance']
~~~

<!-- Ethnio Activation Code -->
<script type="text/javascript" language="javascript" src="//ethn.io/77154.js" async="true" charset="utf-8"></script>