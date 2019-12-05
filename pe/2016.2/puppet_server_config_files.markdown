---
layout: default
title: "Puppet Server configuration files"
canonical: "/pe/latest/puppet_server_config_files.html"
---

This page provides details about the configuration files and settings specific to Puppet Server.

> **Tip:** More information about Puppet Server can be found in [its documentation]({{puppetserver}}/services_master_puppetserver.html). Differences between PE and open source versions of Puppet Server are typically called out.

## `conf.d` directory

At start up, Puppet Server will read all of the `.conf` files found in the `conf.d` directory (`/etc/puppetlabs/puppetserver/conf.d`). This directory contains the following files:

- `global.conf`
- `metrics.conf`
- `pe-puppet-server.conf`
- `webserver.conf`
- `ca.conf` (deprecated)

> **Important:** When making changes to the files in this directory, you must [restart Puppet Server]({{puppetserver}}/restarting.html) in order for the changes to take effect.

### `ca.conf`

This file contains settings related to the Certificate Authority service.

* `certificate-status contains` settings for the certificate_status HTTP endpoint. This endpoint allows certs to be signed, revoked, and deleted via HTTP requests. This provides full control over Puppet's security, and access should almost always be heavily restricted. Puppet Enterprise uses this endpoint to provide a cert signing interface in the PE console.

* `authorization-required` determines whether a client certificate is required to access the certificate status endpoints. If set to `false`, this allows plain text access, and the client-whitelist will be ignored. Defaults to `true`.

* `client-whitelist` contains a list of client certnames that are whitelisted to access to the certificate_status endpoint. Any requests made to this endpoint that do not present a valid client cert mentioned in this list will be denied access.

Here's an example:

    # CA-related settings
    certificate-authority: {
        certificate-status: {
        authorization-required: true
        }
    }

### `global.conf`

This file contains global configuration for Puppet Server. You shouldn't need to make changes to this file. However, you can change the `logging-config` path for the logback logging configuration file (`logback.xml`) if necessary. For more information about `logback.xml`, see the [Puppet Server documentation]({{puppetserver}}/config_file_logbackxml.html).

    global: {

      logging-config: /etc/puppetlabs/puppetserver/logback.xml
      hostname: <the FQDN of your Puppet master>
    }

### `metrics.conf`

This file contains settings related to Puppet Server metrics. For information about graphing Puppet Server metrics, refer to [Graphing Puppet server metrics](puppet_server_metrics.html).

    # metrics-related settings
    metrics: {
        # enable or disable the metrics system
        enabled: true

        # a server id that will be used as part of the namespace for metrics produced
        # by this server
        server-id: master.example.com

        # this section is used to configure reporters that will send the metrics
        # to various destinations for external viewing
        reporters: {

            # enable or disable JMX metrics reporter
            jmx: {
                enabled: true
            }

           # enable or disable graphite metrics reporter
             graphite: {
                enabled: true

           #   # graphite host
               host: "agent1.example.com"
               # graphite metrics port
               port: 2003
               # how often to send metrics to graphite
               update-interval-seconds: 5
           }
        }


### `webserver.conf`

This file contains SSL configuration handled during PE installation, as shown in the following example:

    client-auth : want
    ssl-host    : 0.0.0.0
    ssl-port    : 8140

    ssl-cert    : /etc/puppetlabs/puppet/ssl/certs/master.example.com.pem
    ssl-key     : /etc/puppetlabs/puppet/ssl/private_keys/master.example.com.pem
    ssl-ca-cert : /etc/puppetlabs/puppet/ssl/certs/ca.pem
    ssl-crl-path : /etc/puppetlabs/puppet/ssl/crl.pem

By default, Puppet Server is configured to use the correct Puppet master and CA certificates. If you're using an external CA and have provided your own certificates and keys, make sure `webserver.conf` points to the correct file.

### `pe-puppet-server.conf`

This file contains settings related to Puppet Server.

* The `jruby-puppet` settings configure the interpreter:

  * `gem-home`: This setting determines where JRuby looks for gems. It is also used by the puppetserver gem command line tool. If not specified, uses the Puppet default `/opt/puppetlabs/puppet/cache/jruby-gems`.

  * `master-conf-dir`: Sets the path to the Puppet configuration directory. Uses the PE default `/etc/puppetlabs/puppet`.

  * `master-var-dir`: Sets the path to the Puppet variable directory. Uses the PE default `/opt/puppetlabs/server/data/puppetserver`.

  * `ruby-load-path`: Sets the path where the Puppet Server finds Puppet, Facter, and so on. This path uses the default `/opt/puppetlabs/puppet/lib/ruby/vendor_ruby', '/opt/puppetlabs/puppet/cache/lib'`. The agent's `libdir` value is added into the `ruby-load-path` by default.

  ~~~
    jruby-puppet: {
       gem-home: /var/opt/lib/pe-puppet-server/jruby-gems
       master-conf-dir: /etc/puppetlabs/puppet
       master-var-dir: /var/opt/lib/pe-puppet
       ruby-load-path: ['/opt/puppetlabs/puppet/lib/ruby/vendor_ruby',`/opt/puppetlabs/puppet/cache/lib']
    }
  ~~~
