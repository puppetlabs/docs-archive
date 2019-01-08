---
layout: default
title: "Increasing the ulimit for the PE services"
canonical: "/pe/latest/config_ulimit.html"
---

The various services in PE require up to one file handle per connected client. On most OS configurations, the defaults will not be high enough for more than a couple hundred clients. To support more clients, you need to increate the number of allowed file handles.

You can increase the limits for the following services:

* `pe-orchestration-services`
* `pe-puppetdb`
* `pe-console-services`
* `pe-puppetserver`
* `pe-activemq`
* `pe-puppet`


In each of the following examples, replace `<PE SERVICE>` with the specific service you're editing.

In addition, the "32768" limit can be changed as necessary.

### systemd

With systemd, the number of open file handles allowed is controlled by a setting in the service file at `/usr/lib/systemd/system/<PE SERVICE>.service`. To increase the limit, run the following commands, setting the `LimitNOFILE`as needed:

~~~
mkdir /etc/systemd/system/<PE SERVICE>.service.d
echo "[Service]
 LimitNOFILE=32768" > /etc/systemd/system/<PE SERVICE>.service.d/limits.conf
systemctl daemon-reload
~~~

You can confirm the change by running the following command:

~~~
systemctl show <PE SERVICE> | grep LimitNOFILE
~~~

### upstart

For RedHat and Ubuntu systems, the number of open file handles allowed for is controlled by settings in the following service files:

- Ubuntu: `/etc/default/<PE SERVICE>`
- RedHat: `/etc/sysconfig/<PE SERVICE>`

In both cases, set the last line of the file as follows:

~~~
ulimit -n 32678
~~~

This sets the number of open files allowed at 32,678.

### other init systems

The ulimit controls the number of processes and file handles that the PE service user can open/process. To increase the ulimit for a PE service user, edit `/etc/security/limits.conf` so that it contains the following:

~~~
<PE SERVICE> soft nofile 32768
<PE SERVICE> hard nofile 32768
~~~