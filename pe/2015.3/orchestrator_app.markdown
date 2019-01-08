---
layout: default
title: "Reviewing Applications"
canonical: "/pe/latest/orchestrator_app.html"
---

Use the `puppet app show` command to view the applications you've modeled, as well as the applications you've instantiated, and are thus available to include in an orchestration run.

>**Note**: The first time you run a command, you need to authenticate. See the [Orchestrator client install page](./orchestrator_install.html#set-pe-rbac-permissions-and-token-authorization-for-puppet-orchestrator) for information about setting RBAC permissions and token authorization.

## Run the `puppet app show` Command

- To show an application:

   `puppet app show <APPLICATION> <OPTION>`

- To show an application instance:

   `puppet app show <APPLICATION INSTANCE> <OPTION>`

Without any arguments, the command shows all the application models and their instances found in your default environment (`production`). 
 
#### Command Options

Option | Values | Description
-----------|-----------|-----------
`--environment`, `--env`, `-e` | environment name | Overrides the default environment specified in your Orchestration configuration file.

### Example Output

The following is an example output that shows two instances of an application assigned to nodes, `ExampleApp`. One instance is called `'staging'`, and the other is called `'dev'`.

<a href="./images/orchestrator_app_show.png"><img src="./images/orchestrator_app_show.png" alt="App Show" title="Click to enlarge"> (Click to enlarge)</a>

<!-- Ethnio Activation Code -->
<script type="text/javascript" language="javascript" src="//ethn.io/77154.js" async="true" charset="utf-8"></script>