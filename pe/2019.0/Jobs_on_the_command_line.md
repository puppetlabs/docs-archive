---
author: Isaac Eldridge <isaac.eldridge@puppet.com\>
---

# Running jobs on the command line

The orchestrator on the command line gives you the ability to set up jobs and enforce agent runs against a PQL nodes query, a list of nodes, or application targets. For example, did you just add a new class parameter to a set of nodes, or did you just deploy code to a new environment? Use this command to run Puppet and enforce those changes.

The orchestrator command line tool includes the `puppet job`, `puppet task`, and `puppet app` commands.

-   `puppet job`

    The `puppet job` command is the main mechanism to control Puppet jobs. For a complete reference of the `puppet job` command, see:

    -   Checking job plans on the CLI
    -   Running jobs on the CLI
    -   Reviewing jobs on the CLI
-   `puppet app`

    The `puppet app` tool lets you view the application models and application instances you’ve written and stored on your Puppet master. For a complete reference of the `puppet app` command, see Reviewing applications on the CLI.


