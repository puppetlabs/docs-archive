---
author: Heston Hoffman <heston.hoffman@puppet.com\>
---

# List all database names

Use these instructions to list all database names.

## About this task

To generate a list of database names:

## Procedure

1.  Assume the `pe-postgres` user:

    ```
    sudo su - pe-postgres -s /bin/bash
    ```

2.  Open the PostgreSQL command-line:

    ```
    /opt/puppetlabs/server/bin/psql
    ```

3.  List the databases:

    ```
    \l
    ```

4.  Exit the PostgreSQL command line:

    ```
    \q
    ```

5.  Log out of the `pe-postgres` user:

    ```
    logout
    ```


