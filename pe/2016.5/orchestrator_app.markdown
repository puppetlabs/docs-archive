---
layout: default
title: "Reviewing applications"
canonical: "/pe/latest/orchestrator_app.html"
---

The `puppet app show` command allows you to view applications you've modeled or instantiated and are available to include in an orchestrator run.

>**Note**: The first time you run a command, you need to authenticate. See the [orchestrator installation instructions](./orchestrator_install.html#setting-pe-rbac-permissions-and-token-authentication-for-puppet-orchestrator) for information about setting RBAC permissions and token authorization.

>**Note:** If you're running this command from a managed or non-managed Windows workstation, you must specify the full path to the command. For example, `c:\Program Files\Puppet Labs\Client\bin\puppet-app show`.

## View applications 

Use the `puppet app show` command to view running or completed orchestration jobs.

1. Log into your Puppet master or client tools workstation and run one of the following commands:

   - To show an application:
   
     ~~~
     puppet app show <APPLICATION> <OPTION>
     ~~~

   - To show an application instance:
   
     ~~~
     puppet app show <APPLICATION INSTANCE> <OPTION>
     ~~~
     
   - To show all the application models and their instances found in your default environment (`production`)
   
     ~~~
     puppet app show
     ~~~  

## Command options

Option | Values | Description
-----------|-----------|-----------
`--environment`, `--env`, `-e` | environment name | Overrides the default environment specified in your orchestration configuration file.

## Example output

The following is an example output that shows two instances of an application assigned to nodes, `ExampleApp`. One instance is called `'staging'`, and the other is called `'dev'`.

<a href="./images/orchestrator_app_show.png"><img src="./images/orchestrator_app_show.png" alt="App Show" title="Click to enlarge"> (Click to enlarge)</a>

When you run `puppet app show`, the orchestrator cannot present a completely precise representation of the run order of components (for example, some node runs will start or complete before others). However, `puppet app show` lists the components in an order that respects dependencies between components. Components with dependencies are listed **after* the components on which they depend. 

