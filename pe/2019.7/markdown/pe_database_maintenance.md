# Database maintenance

You can optimize the Puppet Enterprise \(PE\) databases to improve performance.

## Databases in Puppet Enterprise

PE uses PostgreSQL as the backend for its databases. Use the native tools in PostgreSQL to perform database exports and imports.

The PE PostgreSQL database includes the following databases:

|Database|Description|
|--------|-----------|
|pe-activity|Activity data from the Classifier, including user, nodes, and time of activity.|
|pe-classifier|Classification data, all node group information.|
|pe-puppetdb|PuppetDB data, including exported resources, catalogs, facts, and reports.|
|pe-rbac|Role-based access control data, including users, permissions, and AD/LDAP info.|
|pe-orchestrator|Orchestrator data, including user, node, and result details about job runs.|

## Optimize a database

If your databases are slow, begin taking up too much disk space, or need general performance enhancements, use the PostgreSQL `vacuum` command to optimize any of the PE databases.

1.  To optimize a database, run: `su - pe-postgres -s /bin/bash -c "/opt/puppetlabs/server/bin/vacuumdb -z --verbose <DATABASE_NAME>"`


**Related information**  


[List all database names](pe_database_maintenance.md#)

## List all database names

Use these instructions to list all database names.

To generate a list of database names:

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


