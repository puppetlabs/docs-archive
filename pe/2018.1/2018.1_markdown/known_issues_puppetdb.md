# PuppetDB and PostgreSQL known issues

These are the known issues for PuppetDB and PostgreSQL in this release.

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

## Mismatch between classifier classification and matching nodes for regexp rules

PuppetDB’s regular expression matching has surprising behaviors for structured fact value comparisons. For example, the structured fact `os` is a rule that matches `["~", "os", ":"]`. PuppetDB would unintentionally match every node that has the `os` structured fact because the regular expression is applied to the JSON encoded version of the fact value.

The classifier does not use PuppetDB for determining classification and regular expressions in the classifier rules syntax only support direct value comparisons for string types.

This has caused issues in the console where the node list and counts for the "matching nodes" display sometimes indicated that nodes were matching even though the classifier would not consider them matching.

After the fix, the same criteria will be applied to the displays and counts that the classifier uses. The output of the classifier’s rule translation endpoints will also make queries that match the classifier behavior.

**Note:** This fix does not change the way nodes are classified, it only corrects how the GUI displays matching nodes

