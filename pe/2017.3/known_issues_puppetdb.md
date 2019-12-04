# PuppetDB and PostgreSQL known issues

These are the known issues for PuppetDB and PostgreSQL in this PE release.

## Incorrect password for database users causes install/upgrade to fail

If you supply an incorrect password for one of the databases users \(RBAC, console, PuppetDB\), the installation or upgrade fails. However, in some cases it might appear that the installation or upgrade was successful. For example, if the incorrect password is supplied for the console database user, the installation or upgrade continues, and appears to succeed, but the console isn't functional.

## Errors related to stopping `pe-postgresql` service

If for any reason the `pe-postgresql` service is stopped, agents receive several different error messages, for example:

```
Warning: Unable to fetch my node definition, but the agent run will continue:
Warning: Error 400 on SERVER: (<unknown>): mapping values are not allowed in this context at line 7 column 28
```

or, when attempting to request a catalog:

```
Error: Could not retrieve catalog from remote server: Error 400 on SERVER: (<unknown>): mapping values are not allowed in this context at line 7 column 28
Warning: Not using cache on failed catalog
Error: Could not retrieve catalog; skipping run
```

If you encounter these errors, simply re-start the `pe-postgresql` service.

