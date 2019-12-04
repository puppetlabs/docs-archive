---
layout: default
title: "Using the Razor client"
canonical: "/pe/latest/razor_client.html"

---

There are three ways to communicate with the Razor server:

* API calls in JSON sent directly to the server
* JSON arguments sent from the Razor client to the server
* Razor client commands

Razor client commands are the easiest way to interact with the server.

Razor client commands begin with `razor`, followed by the name of the action, like `razor create-repo` or `razor move-policy`. One or more arguments follow the command.

For details about Razor client commands, see [Razor client commands](./razor_client_commands.html). Alternatively, you can access help for each command by entering the command with the `--help` flag, for example `razor add-policy-tag --help`.

## Using positional arguments with Razor client commands

Most Razor client commands allow positional arguments, which means that you don't have to explicitly enter the name of the argument, like `--name`. Instead, you can provide the values for each argument in a specific order.

For example, the `delete-policy` command includes only one argument, `--name`. To delete a policy named `sprocket`, you can enter the command with the argument name and value, or with a positional argument:

* `razor delete-policy --name sprocket` -- command with argument name and value
* `razor delete-policy sprocket` -- command with positional argument

If a command includes multiple options, you can supply from zero to all available positional arguments. For example, the `add_policy_tag` command has three positional arguments: `name`, `tag`, and `rule`. You can provide no positional arguments, `name` only, `name` and `tag` only, or all three arguments.

Because not all arguments are available as positional arguments, you might need to use a combination of positional arguments and name-value pairs. For example, the `create-hook` command has two positional arguments, `name` and `hook-type`, which you might use along with a `--configuration` value: `razor create-hook name_of_hook hook_type --configuration someconfig=value`.

You can switch between positional and non-positional arguments, but you must maintain the expected order for positional arguments. For example, `razor command positional-arg1 --non-positional value positional-arg2 --non-positional2`.

These are the Razor client commands, with available positional arguments listed in accepted order:

Command                         | Positional Arguments
--------------------------------|----------------------------------------
add-policy-tag                  | name, tag, rule
create-broker                   | name, broker-type
create-hook                     | name, hook-type
create-policy                   | name
create-repo                     | name
create-tag                      | name, rule
create-task                     | name
delete-broker                   | name
delete-hook                     | name
delete-node                     | name
delete-policy                   | name
delete-repo                     | name
delete-tag                      | name
disable-policy                  | name
enable-policy                   | name
modify-node-metadata            | node
modify-policy-max-count         | name, max_count
move-policy                     | name
reboot-node                     | name
register-node                   | (none)
reinstall-node                  | name
remove-node-metadata            | node, key
remove-policy-tag               | name, tag
run-hook                        | name
set-node-desired-power-state    | name, to
set-node-hw-info                | node
set-node-ipmi-credentials       | name
update-broker-configuration     | broker, key, value
update-hook-configuration       | hook, key, value
update-node-metadata            | node, key, value
update-policy-repo              | policy, repo
update-policy-task              | policy, task
update-policy-broker            | policy, broker
update-policy-node-metadata     | policy, key, value
update-repo-task                | repo, task
update-tag-rule                 | name, rule


