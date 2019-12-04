---
layout: default
title: "Basic tasks and concepts in Windows"
canonical: "/pe/latest/windows_basic_tasks.html"
---


This page is meant to help familiarize you with several common tasks used in Puppet Enterprise with Windows agents, and explain the concepts and reasons behind performing them. In other guides and walkthroughs in the documentation these can be found as steps in tasks, but they are not explained as thoroughly.

## Developing Puppet manifests

Puppet code is a domain specific language (DSL) that defines the desired state of resources on a system, such as files, users, and packages. Puppet manifest files are lists of resources that have a unique title and a set of named attributes that describe the desired state. Puppet compiles these text-based manifests into catalogs, and uses those to apply configuration changes.

Use a text editor to create Puppet manifest files. [Atom](https://atom.io/), [Visual Studio Code](https://code.visualstudio.com/) and [Sublime Text](http://www.sublimetext.com/) support syntax highlighting of the Puppet language. Editors like [Notepad++](https://notepad-plus-plus.org/) or Notepadwon't highlight Puppet syntax, but may be used to create manifests as well.

### Task 1: Write a simple manifest

1. Create a file named `file.pp` and save it in `c:\myfiles\`.
2. With your text editor of choice, add the following text to the file:

~~~ruby
file { 'c:\\Temp\\foo.txt':
  ensure   => present,
  content  => 'This is some text in my file'
}
~~~

Note the following details in this file resource example:

* Puppet uses a basic syntax of `type { title: }`, where `type` is the resource type --- in this case it's `file`.
* The resource title (the value before the `:`) is `C:\\Temp\\foo.txt`. The file resource uses the title to determine where to create the file on disk. A resource title must always be unique within a given manifest.
* `ensure` is set to `present` to create the file on disk, if it's not already present. For file type resources, Puppet also supports the value `absent`, which removes the file from disk if it exists.
* `content` is set to `This is some text in my file`, which writes that value to the file.

For more information about all of the resource types in Puppet, see the [Resource Type Reference]({{puppet}}/type.html).

## Launching the Puppet Command Prompt

Next, access Puppet's command line tools. To open the command line interface for these tools, enter `Command Prompt Puppet` in your **Start Menu**, and click **Start Command Prompt with Puppet**. 

The Puppet command prompt has a few details worth noting:

* Several important batch files live in the current working directory, `C:\Program Files\Puppet Labs\Puppet\bin`. The most important of these batch files is `puppet.bat`. Puppet is a Ruby based application, and `puppet.bat` is a wrapper around executing Puppet code through ruby.exe.
* Running the Puppet Command Prompt ensures that all of the Puppet tooling is in `PATH`, even if you change to a different directory.


## Applying manifests

Next, we'll show how to use the `puppet apply` command line tool to enforce the state described in the manifest, `c:\myfiles\file.pp`. However, before you ask Puppet to enforce these changes, learn about two other useful operations on the manifest.

### Task 2: Validate your manifest with `puppet parser validate`

You can validate that a manifest's syntax is correct by using the command `parser validate`:

1. Check your syntax by entering `puppet parser validate c:\myfiles\file.pp` in the Puppet Command Prompt. If a manifest has no syntax errors, the tool outputs nothing.

2. To see what output occurs when there is an error, temporarily edit the manifest and remove the `:` after the resource title. Run `puppet parser validate c:\myfiles\file.pp` again, and see the following output:

~~~
Error: Could not parse for environment production: Syntax error at 'ensure' at c:/myfiles/file.pp:2:3
~~~


### Task 3: Simulate a Puppet run with `--noop`

Next, simulate the changes that Puppet would make were it to apply this manifest. Puppet has a switch that you can use to test if manifests will make the intended changes. This is referred to as non-enforcement or no-op mode.

To simulate changes, run `puppet apply c:\myfiles\file.pp --noop` in the command prompt:

~~~
C:\Program Files\Puppet Labs\Puppet\bin>puppet apply c:\myfiles\file.pp --noop
Notice: Compiled catalog for win-User.localdomain in environment production in 0.45 seconds
Notice: /Stage[main]/MainFile[C:\Temp\foo.txt]/ensure: current value absent, should be present (noop)
Notice: Class[Main]: Would have triggered 'refresh' from 1 events
Notice: Stage[main]: Would have triggered 'refresh' from 1 events
Notice: Applied catalog in 0.03 seconds
~~~

Puppet shows you the changes it *would* make, but does not actually make the changes. It *would* create a new file at `C:\Temp\foo.txt`, but it hasn't, because you used `--noop`.

### Task 4: Enforce the desired state with `puppet apply`

When the output of the simulation shows the changes you intend to make, you can start enforcing these changes with the `puppet apply` command.

Run `puppet apply c:\myfiles\file.pp`. Puppet enforces the resource state you've described in `file.pp`, in this case guaranteeing that a file (`c:\Temp\foo.txt`) is present and has the contents `This is some text in my file`.


To see more details about what Puppet did, you can specify additional options, such as `--trace`, `--debug`, or `--verbose`, which can help you diagnose problematic Puppet code. If `puppet apply` fails, Puppet outputs a full stack trace.


## Understanding idempotency

A key feature of Puppet is its *idempotency*: the ability to repeatedly apply a manifest to guarantee a desired resource state on a system, with the same results every time. If a given resource is already in the desired state, Puppet performs no actions. If a given resource is not in the desired state, Puppet takes whatever action is necessary to put the resource into the desired state. Idempotency enables Puppet to simulate resource changes without performing them, and lets you set up config management one time, fixing configuration drift without recreating resources from scratch each time Puppet runs.

To demonstrate how Puppet can be applied repeatedly to get the same results, change the manifest at `c:\myfiles\file.pp` to the following:

~~~
file { 'C:\\Temp\\foo.txt':
  ensure   => present,
  content  => 'I have changed my file content.'
}
~~~

Apply the manifest by running `puppet apply c:\myfiles\file.pp`. Open `c:\Temp\foo.txt` and notice that Puppet changes the file's contents.


Applying the manifest again with `puppet apply c:\myfiles\file.pp` results in no changes to the system, demonstrating that Puppet behaves idempotently.

Many of the samples in Puppet documentation assume that you have this basic understanding of creating and editing manifest files, and applying them with `puppet apply`.


## Additional command line tools

### `puppet agent`

Like `puppet apply`, the `puppet agent` command line tool applies configuration changes to a system. However, `puppet agent` retrieves compiled catalogs from a Puppet Server, and applies them to the local system. Puppet is installed as a Windows service, and by default tries to contact the master every 30 minutes by running `puppet agent` to retrieve new catalogs and apply them locally.

### `puppet resource`

You can run `puppet resource` to query the state of a particular type of resource on the system. For example, to list all of the users on a system, run the command `puppet resource user`.

![Puppet resource user](images/windows/puppet_resource_user_output.png)

The computer used for this example has three local user accounts: Administrator, Guest, and vagrant. Note that the output is the same format as a manifest, and you can copy and paste it directly into a manifest.


### `puppet module install`

Puppet includes many core resource types, plus you can extend Puppet by installing Puppet modules. Puppet modules contain additional resource definitions and the code necessary to modify a system to create, read, modify, or delete those resources. The [Puppet Forge](https://forge.puppetlabs.com/) contains modules developed by Puppet and community members available for anyone to use.

Puppet synchronizes modules from a master to agent nodes during `puppet agent` runs. Alternatively, you can use the standalone Puppet Module Tool, included when you install Puppet, to manage, view, and test modules.

Run `puppet module list` to show the list of modules installed on the system.

To install modules, the Puppet Module Tool uses the syntax `puppet module install NAMESPACE/MODULENAME`.  The `NAMESPACE` is registered to a module, and `MODULE` refers to the specific module name.  A very common module to install on Windows is `registry`, under the `puppetlabs` namespace. So, to install the `registry` module, run `puppet module install puppetlabs/registry`.

For a more in-depth walkthrough, read the documentation on [installing and using Windows modules](./windows_modules.html).