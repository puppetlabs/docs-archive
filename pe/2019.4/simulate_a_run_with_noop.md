# Simulate a Puppet run with `--noop`

Puppet has a switch that you can use to test if manifests make the intended changes. This is referred to as non-enforcement or no-op mode.

1.  To simulate changes, run `puppet apply c:\myfiles\file.pp --noop` in the command prompt:

    ```
    C:\Program Files\Puppet Labs\Puppet\bin>puppet apply c:\myfiles\file.pp --noop
    Notice: Compiled catalog for win-User.localdomain in environment production in 0.45 seconds
    Notice: /Stage[main]/MainFile[C:\Temp\foo.txt]/ensure: current value absent, should be present (noop)
    Notice: Class[Main]: Would have triggered 'refresh' from 1 events
    Notice: Stage[main]: Would have triggered 'refresh' from 1 events
    Notice: Applied catalog in 0.03 seconds
    ```


Puppet shows you the changes it *would* make, but does not actually make the changes. It *would* create a new file at `C:\Temp\foo.txt`, but it hasn't, because you used `--noop`.

**Parent topic:**[Practice tasks](practice_windows_tasks.md)

