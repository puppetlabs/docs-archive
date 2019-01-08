---
layout: default
title: "Tasks"
canonical: "/pe/latest/razor_tasks.html"

---

Tasks describe a process or collection of actions that should be performed
while Razor provisions machines. Tasks can be used to designate an
operating system or other software that should be installed, where to get
it, and the configuration details for the installation.

Tasks are structurally simple. They consist of a YAML metadata file and any
number of ERB templates. You include the tasks you want to run in your
policies (policies are described in the next section). The templates are
used to generate things like the iPXE script that will boot a node into the
installer, and automated installation files like kickstart, preseed or
unattended files.

Razor provides a handful of
[existing tasks](https://github.com/puppetlabs/razor-server/tree/master/tasks),
or you can create your own. The existing tasks are primarily for installing
supported operating systems.

Tasks are stored in the file system. The configuration setting `task_path`
determines where in the file system Razor looks for tasks and can be a
colon-separated list of paths. Relative paths in that list are taken to be
relative to the top-level Razor directory. For example, setting `task_path`
to `/opt/puppet/share/razor-server/tasks:/home/me/task:tasks` will make
Razor search these three directories in that order for tasks.

## Task metadata

Tasks can include the following metadata in the task's YAML file. This file
is called `metadata.yaml` and exists in `tasks/<NAME>.task` where `NAME` is
the task name. Therefore, the task name looks like this:
`tasks/<NAME>.task/metadata.yaml`.

~~~
---
description: HUMAN READABLE DESCRIPTION
os: OS NAME
os_version: OS_VERSION_NUMBER
base: TASK_NAME
boot_sequence:
  1: boot_templ1
  2: boot_templ2
  default: boot_local
~~~

Only `os_version` and `boot_sequence` are required. The `base` key allows
you to derive one task from another by reusing some of the `base` metadata
and templates. If the derived task has metadata that's different from the
metadata in `base`, the derived metadata overrides the base task's
metadata.

The `boot_sequence` hash indicates which templates to use when a node using
this task boots. In the example above, a node will first boot using
`boot_templ1`, then using `boot_templ2`. For every subsequent boot, the
node will use `boot_local`.

## Writing task templates

Task templates are ERB templates and are searched in all the directories
given in the `task_path` configuration setting. Templates are searched in
the subdirectories in this order:

1. `name.task`
2. `base.task` # If the task has a base task.
3. `common`

### Template helpers

Templates can use the following helpers to generate URLs that point back to
the server; all of the URLs respond to a `GET` request, even the ones that
make changes on the server:

* `task`: Includes attributes such as `name`, `os`, `os_version`, `boot_seq`, `label`, `description`, `base`, and `architecture`.
* `node`: Includes attributes such as `name`, `metadata`, and `facts`.
* `repo`: Includes attributes such as `name`, `iso_url`, `url`.
* `file_url(TEMPLATE)`: The URL that will retrieve `TEMPLATE.erb` (after evaluation) from the current node's task.
* `repo_url(PATH)`: The URL to the file at `PATH` in the current repo.
* `log_url(MESSAGE, SEVERITY)`: The URL that will log `MESSAGE` in the current node's log.
* `node_url`: The URL for the current node.
* `store_url(VARS)`: The URL that will store the values in the hash `VARS` in the node. Currently only changing the node's IP address is supported. Use `store_url("ip" => "192.168.0.1")` for that.
* `stage_done_url`: The URL that tells the server that this stage of the boot sequence is finished, and that the next boot sequence should begin upon reboot.
* `broker_install_url`: A URL from which the install script for the node's broker can be retrieved. You can see an example in the script, [os_complete.erb](https://github.com/puppetlabs/razor-server/blob/master/tasks/common/os_complete.erb), which is used by most tasks.

Each boot (except for the default boot) must culminate in something akin to
`curl <%= stage_done_url %>` before the node reboots. Omitting this will
cause the node to reboot with the same boot template over and over again.

The task must indicate to the Razor server that it has successfully
completed by doing a `GET` request against `stage_done_url("finished")`,
for example using `curl` or `wget`. This will mark the node `installed` in
the Razor database.

You use these helpers by causing your script to perform an `HTTP GET`
against the generated URL. This might mean that you pass an argument like
`ks=<%= file_url("kickstart")%>` when booting a kernel, or that you put
`curl <%= log_url("Things work great") %>` in a shell script.

**Related links**:

* [Task client commands](./razor_client_commands.html#task-commands)
* [Task APIs](./razor_reference.html#tasks)

* * *

