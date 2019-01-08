---
layout: default
title: "Reviewing applications"
canonical: "/pe/latest/orchestrator_app.html"
---

Use the `puppet app show` command to view the applications you've modeled, as well as the applications you've instantiated, and are thus available to include in an orchestration run.

>**Note**: The first time you run a command, you need to authenticate. See the [orchestrator client install page](./orchestrator_install.html#set-pe-rbac-permissions-and-token-authentication-for-puppet-orchestrator) for information about setting RBAC permissions and token authorization.

## Run the `puppet app show` command

- To show an application:

   `puppet app show <APPLICATION> <OPTION>`

- To show an application instance:

   `puppet app show <APPLICATION INSTANCE> <OPTION>`

Without any arguments, the command shows all the application models and their instances found in your default environment (`production`).

#### Command options

Option | Values | Description
-----------|-----------|-----------
`--environment`, `--env`, `-e` | environment name | Overrides the default environment specified in your orchestration configuration file.

### Example output

The following is an example output that shows two instances of an application assigned to nodes, `ExampleApp`. One instance is called `'staging'`, and the other is called `'dev'`.

<a href="./images/orchestrator_app_show.png"><img src="./images/orchestrator_app_show.png" alt="App Show" title="Click to enlarge"> (Click to enlarge)</a>

When you run `puppet app show`, the orchestrator cannot present a completely precise representation of the run order of components (for example, some node runs will start or complete before others). However, `puppet app show` lists the components in an order that respects dependencies between components. Components with dependencies are listed **after* the components on which they depend. 

