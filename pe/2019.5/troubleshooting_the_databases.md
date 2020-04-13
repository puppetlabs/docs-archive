# Troubleshooting the databases

If you have issues with the databases that support the console, make sure that the PostgreSQL database is not too large or using too much memory, that you don't have port conflicts, and that `puppet apply` is configured correctly.

**Note:** If you're using your own instance of PostgreSQL for the console and PuppetDB, you must use version 9.1 or higher.

**Related information**  


[Install using text install](installing_pe.md#)

## PostgreSQL is taking up too much space

The PostgreSQL `autovacuum=on` setting prevents the database from growing too large and unwieldy. Routine vacuuming is turned on by default.

### About this task

### Procedure

1.  Verify that `autovacuum` is set to `on`.


## PostgreSQL buffer memory causes installation to fail

When installing PE on machines with large amounts of RAM, the PostgreSQL database might use more shared buffer memory than is available.

### About this task

If this issue is present, `/var/log/pe-postgresql/pgstartup.log` shows the error:

```
FATAL: could not create shared memory segment: No space left on device
DETAIL: Failed system call was shmget(key=5432001, size=34427584512,03600).
```

### Procedure

1.  On the master, set the `shmmax` kernel setting to approximately 50% of the total RAM.

2.  Set the `shmall` kernel setting to the quotient of the new `shmmax` setting divided by the page size. You can confirm page size by running `getconf PAGE_SIZE`.

3.  Set the new kernel settings:

    ```
    sysctl -w kernel.shmmax=<your shmmax calculation>
    sysctl -w kernel.shmall=<your shmall calculation>
    ```


## The default port for PuppetDB conflicts with another service

By default, PuppetDB communicates over port 8081. In some cases, this might conflict with existing services, for example McAfee ePolicy Orchestrator.

### About this task

### Procedure

1.  Install in text mode with a parameter in `pe.conf` that specifies a different port using `puppet_enterprise::puppetdb_port`.


## `puppet resource` generates Ruby errors after connecting `puppet apply` to PuppetDB

If Puppet apply is configured incorrectly, for example by modifying `puppet.conf` to add the parameters `storeconfigs_backend = puppetdb` and `storeconfigs = true` in both the main and master sections, `puppet resource` ceases to function and displays a Ruby run error.

### About this task

### Procedure

1.  Modify `/etc/puppetlabs/puppet/routes.yaml` to correctly [connect Puppet apply](https://docs.puppet.com/puppetdb/5.1/connect_puppet_apply.html) without affecting other functions.


