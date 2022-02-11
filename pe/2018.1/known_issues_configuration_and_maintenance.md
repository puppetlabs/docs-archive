---
author: Melissa Amos <melissa.amos@puppet.com\>
---

# Configuration and maintenance known issues

These are the known issues for configuration and maintenance in this release.

## `puppet infrastructure recover_configuration` misreports success if specified environment doesn't exist

If you specify an invalid environment when running `puppet infrastructure recover_configuration`, the system erroneously reports that the environment's configuration was saved.

## Restoring the `pe-rbac` database fails with the `puppet-backup restore` command

When restoring the `pe-rbac` database, the restore process exits with errors about a duplicate operator family, `citext_ops`.

To work around this issue:

1.  Log into your PostgreSQL instance:

    ```
    sudo su - pe-postgres -s /bin/bash -c "/opt/puppetlabs/server/bin/psql pe-rbac"
    ```

2.  Run these commands:

    ```
    ALTER EXTENSION citext ADD operator family citext_ops using btree;
    ALTER EXTENSION citext ADD operator family citext_ops using hash;
    ```

3.  Exit the PostgreSQL shell and re-run the backup utility.

## `puppet-backup` fails if gems are missing from the master's agent environment

The `puppet-backup` create command might fail if any gem installed on the Puppet Server isn't present on the agent environment on the master. If the gem is either absent or of a different version on the master's agent environment, you'll get the error "command `puppet infrastructure recover_configuration` failed".

To fix this, install missing or incorrectly versioned gems on the master's agent environment. To find which gems are causing the error, check the backup logs for gem incompatibility issues with the error message. PE creates backup logs as a `report.txt` whenever you run a `puppet-backup` command.

To see what gems and their versions you have installed on your Puppet Server, run the command `puppetserver gem list` . To see what gems are installed in the agent environment on your master, run `/opt/puppetlabs/puppet/bin/gem list`.

