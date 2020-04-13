---
author: Mindy Moreland <mindy.moreland@puppet.com\>
---

# Troubleshooting `puppet infrastructure run` commands

If `puppet infrastructure run` commands fail, review the logs at `/var/log/puppetlabs/installer/bolt_info.log` and check for these issues.

## Running commands when logged in as a non-root user

All `puppet infrastructure run` commands require you to act as the root user on all nodes that the command touches. If you are trying to run a `puppet infrastructure run` command as a non-root user, you must be able to SSH to the impacted nodes \(as the same non-root user\) in order for the command to succeed.

When you run a `puppet infrastructure run` command, Bolt uses your system's existing OpenSSH `ssh_config` configuration file to connect to your nodes. If this file is missing or configured incorrectly, Bolt tries to connect as root. To make sure the correct user connects to the nodes, you have the following options.

1.  Set up your OpenSSH `ssh_config` configuration file to point to a user with sudo privileges:

    ```
    Host *.example.net
      UserKnownHostsFile=~/.ssh/known_hosts
      User <USER WITH SUDO PRIVELEGES>
    ```

2.  When running a `puppet infrastructure run` command, pass in the `--user <USER WITH SUDO PRIVELEGES>` flag.

If your sudo configuration requires a password to run commands, pass in the `--sudo-password <PASSWORD>` flag when running a `puppet infrastructure run` command.

**Note:** To avoid logging your password to `.bash_history`, set `HISTCONTROL=ignorespace` in your `.bashrc` file and add a space to the beginning of the command.

If your operating system distribution includes the `requiretty` option in its `/etc/sudoers` file, you must either remove this option from the file or pass the `--tty` flag when running a `puppet infrastructure run` command.

## Passing hashes from the command line

When passing a hash on the command line as part of a `puppet infrastructure run` command, the hash must be quoted, much like a JSON object. For example:

```
'{"parameter_one": "value_one", “parameter_two”: “value_two”}'
```

