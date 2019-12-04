---
layout: default
title: "Configuring ulimit for PE services"
canonical: "/pe/latest/config_ulimit.html"
---


As your PE infrastructure grows and you bring more agents under management, you may need to increase the number allowed file handles per client.

## Configure ulimit for PE services

The various services in PE require up to one file handle per connected client. On most OS configurations, the defaults will not be high enough for more than a couple hundred clients. To support more clients, you need to increate the number of allowed file handles.

You can increase the limits for the following services:

* `pe-orchestration-services`
* `pe-puppetdb`
* `pe-console-services`
* `pe-puppetserver`
* `pe-activemq`
* `pe-puppet`

The location and method for configuring ulimit depends on your agent's platform. You may need to use systemd, upstart, or some other init system.

> **Note:** In each of the following examples, replace `<PE SERVICE>` with the specific service you're editing. In addition, the example "32768" limit can be changed as necessary.

### systemd

With systemd, the number of open file handles allowed is controlled by a setting in the service file at `/usr/lib/systemd/system/<PE SERVICE>.service`. 

1. To increase the limit, run the following commands, setting the `LimitNOFILE`as needed:

   ~~~
   mkdir /etc/systemd/system/<PE SERVICE>.service.d
   echo "[Service]
   LimitNOFILE=32768" > /etc/systemd/system/<PE SERVICE>.service.d/limits.conf
   systemctl daemon-reload
   ~~~

2. You can confirm the change by running the following command:

   ~~~
   systemctl show <PE SERVICE> | grep LimitNOFILE
   ~~~

### upstart

For RedHat and Ubuntu systems, the number of open file handles allowed for is controlled by settings in the following service files:

- Ubuntu: `/etc/default/<PE SERVICE>`
- RedHat: `/etc/sysconfig/<PE SERVICE>`

1. In both cases, set the last line of the file as follows:

   ~~~
   ulimit -n 32678
   ~~~

This sets the number of open files allowed at 32,678.

### other init systems

The ulimit controls the number of processes and file handles that the PE service user can open/process. 

1. To increase the ulimit for a [PE service user](./install_what_and_where.html#user-accounts), edit `/etc/security/limits.conf` so that it contains the following:

   ~~~
   <PE SERVICE USER> soft nofile 32768
   <PE SERVICE USER> hard nofile 32768
   ~~~