# Launch the Puppet command prompt

A lot of common interactions with Puppet are done via the command line.

1.  To open the command line interface, enterCommand Prompt Puppet in your **Start Menu**, and click **Start Command Prompt with Puppet**.


The Puppet command prompt has a few details worth noting:

-   Several important batch files live in the current working directory, `C:\Program Files\Puppet Labs\Puppet\bin`. The most important of these batch files is `puppet.bat`. Puppet is a Ruby based application, and `puppet.bat` is a wrapper around executing Puppet code through `ruby.exe`.
-   Running the command prompt with Puppet rather than just the default Windows command prompt ensures that all of the Puppet tooling is in PATH, even if you change to a different directory.


**Parent topic:**[Practice tasks](practice_windows_tasks.md)

