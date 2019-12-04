---
layout: default
title: "Troubleshooting MCollective"
canonical: "/pe/latest/trouble_orchestration.html"
---

Increasing the ulimit for the `pe-activemq` user
--------

The ulimit controls the number of processes and file handles that the `pe-activemq` user can open/process. To increase the ulimit for the `pe-activemq` user, edit `/etc/security/limits.conf` so that it contains the following:

    pe-activemq   soft     nproc  8192
    pe-activemq   hard     nproc  8192
    pe-activemq   soft     nofile 16384
    pe-activemq   hard     nofile 16384

Accessing the ActiveMQ console
----------

In some cases, you may need to access the ActiveMQ console to troubleshoot messages, which are handled by the `pe-activemq` service. To do this, you will need to enable the ActiveMQ console from within the PE console by editing the `enable_web_console` parameter of the `puppet_enterprise::profile::amq::broker` class. The ActiveMQ node can be reached from whichever node has the `puppet_enterprise::profile::amq::broker` class.

To activate the ActiveMQ console:

1. **On the Puppet master**, navigate to `/etc/puppetlabs/activemq/jetty.xml`.

2. Edit `jetty.xml` so that the `"host"` value of the `"connectors"` property is set to `"0.0.0.0"`, as shown in the following example:

   ~~~
   <bean id="jettyPort" class="org.apache.activemq.web.WebConsolePort" init-method="start">
   <!-- the default port number for the web console -->
   <property name="host" value="0.0.0.0"/>
   <property name="port" value="8161"/>
   </bean>
   ~~~

3. In the console, click **Classification**, and in the **PE Infrastructure** node group, select the **PE ActiveMQ Broker** node group.

4. On the **Classes** tab, under the `puppet_enterprise::profile::amq::broker` class, set the `enable_web_console` parameter to `true`.

5. Click **Add parameter**, and commit changes.

6. Run Puppet on the agents in the **PE ActiveMQ Broker** group.

You can reach the the ActiveMQ console from whichever node has the `puppet_enterprise::profile::amq::broker` class on port 8161 with `/admin` (e.g., `http://hostname:8161/admin`).

The password and username used to log into the ActiveMQ console is defined at `/etc/puppetlabs/activemq/jetty-realm.properties`.


