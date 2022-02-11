---
author: Suzie Baunsgard <suzanne@puppet.com\>
---

# Optimize a database

If your databases are slow, begin taking up too much disk space, or need general performance enhancements, use the PostgreSQL `vacuum` command to optimize any of the PE databases.

## Procedure

1.  To optimize a database, run: `su - pe-postgres -s /bin/bash -c "/opt/puppetlabs/server/apps/postgresql/bin/vacuumdb -z --verbose <DATABASE NAME>"`


## Results

**Related information**  


[List all database names](generate_a_list_of_database_names.md)

