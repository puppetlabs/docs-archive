# Troubleshooting backup and restore

If backup or restore fails, check for these issues.

## The `puppet-backup restore` command fails with errors about a duplicate operator family

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

