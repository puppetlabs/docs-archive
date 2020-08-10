# Configuring ulimit for PE services

As your infrastructure grows and you bring more agents under management, you might need to increase the number allowed file handles per client.

**Related information**  


[Methods for configuring Puppet Enterprise](config_intro.md#)

## Configure ulimit for PE services

The various services in PE require up to one file handle per connected client. On most operating system configurations, the defaults are not adequate for more than a couple hundred clients. To support more clients, you need to increase the number of allowed file handles.

You can increase the limits for the following services:

-   `pe-orchestration-services`
-   `pe-puppetdb`
-   `pe-console-services`
-   `pe-puppetserver`
-   `pe-puppet`

The location and method for configuring ulimit depends on your agent’s platform. You might use systemd, upstart, or some other init system.

In the following instructions, replace `<PE SERVICE>` with the specific service you’re editing. The examples show setting a limit of `32768`, which you can also change according to what you need.

### Configure ulimit using systemd

With systemd, the number of open file handles allowed is controlled by a setting in the service file at `/usr/lib/systemd/system/<PE SERVICE>.service`.

1.  To increase the limit, run the following commands, setting the `LimitNOFILE` value to the new number:

    ```
    mkdir /etc/systemd/system/<PE SERVICE>.service.d
    echo "[Service]
    LimitNOFILE=32768" > /etc/systemd/system/<PE SERVICE>.service.d/limits.conf
    systemctl daemon-reload
    ```

2.  Confirm the change by running: `systemctl show <PE SERVICE> | grep LimitNOFILE`


### Configure ulimit using upstart

For Ubuntu and Red Hat systems, the number of open file handles allowed for is controlled by settings in service files.

The service files are:

-   Ubuntu: `/etc/default/<PE SERVICE>`
-   Red Hat: `/etc/sysconfig/<PE SERVICE>`

1.  For both Ubuntu and Red Hat, set the last line of the file as follows:

    `ulimit -n 32678`


This sets the number of open files allowed at 32,678.

### Configure ulimit on other init systems

The ulimit controls the number of processes and file handles that the PE service user can open and process.

To increase the ulimit for a PE service user: 

1.  Edit `/etc/security/limits.conf` so that it contains the following lines:

    ```
    <PE SERVICE USER> soft nofile 32768
    <PE SERVICE USER> hard nofile 32768
    ```


