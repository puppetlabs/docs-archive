# Troubleshooting MCollective

If you have performance issues with the ActiveMQ service, you can increase the ulimit or use the ActiveMQ console to get troubleshooting information.

## ActiveMQ generates errors about too many open files or not enough memory

The ulimit must be adequate for the number of files \(`nofile`\) and number of processes \(`nproc`\) handled in your environment.

### About this task

### Procedure

1.  To increase the ulimit for the `pe-activemq` user, edit `/etc/security/limits.conf` so that it contains:

    ```
    pe-activemq   soft     nproc  8192
    pe-activemq   hard     nproc  8192
    pe-activemq   soft     nofile 16384
    pe-activemq   hard     nofile 16384
    ```


## ActiveMQ doesn't perform as expected

The ActiveMQ service provides a console that can be used for advanced debugging, such as pulling performance metrics from the AMQ service while it's running. The console must be configured for access.

### About this task

### Procedure

1.  On the Puppet master, edit `/etc/puppetlabs/activemq/jetty.xml` so that the `"host"` value of the `"connectors"` property is set to \`"0.0.0.0". For example:

    ```
    <bean id="jettyPort" class="org.apache.activemq.web.WebConsolePort" init-method="start">
    <!-- the default port number for the web console -->
    <property name="host" value="0.0.0.0"/>
    <property name="port" value="8161"/>
    </bean>
    
    ```

2.  In the console, click **Classification**, and in the **PE Infrastructure** node group, select the **PE ActiveMQ Broker** node group.

3.  On the **Configuration** tab, under the `puppet_enterprise::profile::amq::broker` class, set the `enable_web_console` parameter to `true`.

4.  Click **Add parameter** and commit changes.

5.  Run Puppet on the agents in the **PE ActiveMQ Broker** group.


### Results

Access the ActiveMQ console from the node with the `puppet_enterprise::profile::amq::broker` class on port 8161 with `/admin`, for example, `http://hostname:8161/admin`. The password and username for the ActiveMQ console is defined at `/etc/puppetlabs/activemq/jetty-realm.properties`.

