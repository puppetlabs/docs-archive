# Troubleshooting connections between components

If agent nodes can't retrieve configurations, check for communication, certificate, DNS , and NTP issues.

## Agents can't reach the Puppet master

Agent nodes must be able to communicate with the Puppet master in order to retrieve configurations.

### About this task

If agents can't reach the Puppet master, running `telnet <puppet master's hostname> 8140` returns the error "Name or service not known."

### Procedure

1.  Verify that the Puppet master server is reachable at a DNS name your agents recognize.

2.  Verify that the `pe-puppetserver` service is running.


## Agents don't have signed certificates

Agent certificates must be signed by the Puppet master.

### About this task

If the node's Puppet agent logs have a warning about unverified peer certificates in the current SSL session, the agent has submitted a certificate signing request that hasn't yet been signed.

### Procedure

1.  On the master, view a list of pending certificate requests: `puppet cert list`

2.  Sign a specified node's certificate: `puppet cert sign <NODE NAME>`


## Agents aren't using the master's valid DNS name

Agents trust the master only if they contact it at one of the valid hostnames specified when the master was installed.

### About this task

On the node, if the results of `puppet agent --configprint server` don't return one of the valid DNS names you chose during installation of the master, the node and master can't establish communication.

### Procedure

1.  To edit the master's hostname on nodes, in `/etc/puppetlabs/puppet/puppet.conf`, change the `server` setting to a valid DNS name.

2.  To reset the master's valid DNS names, run:

    ```
    /etc/init.d/pe-nginx stop
    puppet cert clean <MASTER_CERTNAME>
    puppet cert generate <MASTER_CERTNAME> --dns_alt_names=<COMMA-SEPARATED_LIST_OF_DNS_NAMES>
    /etc/init.d/pe-nginx start
    ```


## Time is out of sync

The date and time must be in sync on the Puppet master and agent nodes.

### About this task

If time is out of sync on nodes, running `date` returns incorrect or inconsistent dates.

### Procedure

1.  Get time in sync by setting up NTP. Keep in mind that NTP can behave unreliably on virtual machines.


## Node certificates have invalid dates

The date and time must be in sync when certificates are created.

### About this task

If certificates were signed out of sync, running `openssl x509 -text -noout -in $(puppet config print --section master ssldir)/certs/<NODE NAME>.pem` returns invalid dates, such as certificates dated in the future.

### Procedure

1.  On the master, delete certificates with invalid dates: `puppet cert clean <NODE NAME>`

2.  On nodes with invalid certificates, delete the SSL directory: `rm -r $(puppet config print --section master ssldir`

3.  On agent nodes, generate a new certificate request: `puppet agent --test`

4.  On the master, sign the request: `puppet cert sign <NODE NAME>`


## A node is re-using a certname

If a node re-uses an old node's certname and the master retains the previous node's certificate, the new node is unable to request a new certificate.

### About this task

### Procedure

1.  On the master, clear the node's certificate: `puppet cert clean <NODE NAME>`

2.  On agent node, generate a new certificate request: `puppet agent --test`

3.  On the master, sign the request: `puppet cert sign <NODE NAME>`


## Agents can't reach the filebucket server

If the master is installed with a certname that doesn't match its hostname, agents can't back up files to the filebucket on the Puppet master.

### About this task

If agents log errors like "could not back up," nodes are likely attempting to back up files to the wrong hostname.

### Procedure

1.  On the master, edit `/etc/puppetlabs/code/environments/production/manifests/site.pp` so that filebucket `server` attribute points to the correct hostname:

    ```ruby
    # Define filebucket 'main':
    filebucket { 'main':
      server => '<PUPPET_MASTER_DNS_NAME>',
      path   => false,
    }
    ```


### Results

Changing the filebucket server attribute on the master fixes the error on all agent nodes.

## Orchestrator can't connect to PE Bolt server

Debug a faulty connection between the orchestrator and PE Bolt server by setting the `bolt_server_loglevel` in the `puppet_enterprise::profile::bolt_server` class and running Puppet, or by manually updating `loglevel` in `/etc/puppetlabs/bolt-server/conf.d/bolt-server.conf`. The server logs are located at `/var/log/puppetlabs/bolt-server/bolt-server.log`.

