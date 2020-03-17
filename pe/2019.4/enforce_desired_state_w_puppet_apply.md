# Enforce the desired state with `puppet apply`

When the output of the simulation shows the changes you intend to make, you can start enforcing these changes with the `puppet apply` command.

1.  Run `puppet apply c:\myfiles\file.pp`.

    To see more details about what this command did, you can specify additional options, such as `--trace`, `--debug`, or `--verbose`, which can help you diagnose problematic code. If `puppet apply` fails, Puppet outputs a full stack trace.


Puppet enforces the resource state you've described in `file.pp`, in this case guaranteeing that a file \(`c:\Temp\foo.txt`\) is present and has the contents `This is some text in my file`.

**Parent topic:**[Practice tasks](practice_windows_tasks.md)

