---
layout: default
title: "Troubleshooting connections between components "
canonical: "/pe/latest/trouble_comms.html"
---

Below are some common issues that can prevent the different parts of Puppet Enterprise from communicating with each other.

Agent nodes can't retrieve their configurations
-----

### Is the Puppet master reachable from the agents?

Although this would probably have caused a problem during installation, it's worth checking it first. You can check whether the master is reachable and active by trying:

    $ telnet <puppet master's hostname> 8140

If the Puppet master is alive and reachable, you'll get something like:

    Trying 172.16.158.132...
    Connected to screech.example.com.
    Escape character is '^]'.

Otherwise, it will return something like "name or service not known."

To fix this, make sure the Puppet master server is reachable at the DNS name your agents know it by and make sure that the `pe-puppetserver` service is running.

<!-- ### Can the Puppet master reach the console?

The Puppet master depends on the console for the names of the classes an agent node should get. If it can't reach the console, it can't compile configurations for nodes.

Check the [Puppet agent logs](/pe/latest/install_what_and_where.html#puppet-agent-logs) on your nodes, or run `puppet agent --test` on one of them; if you see something like `err: Could not retrieve catalog from remote server: Error 400 on SERVER: Could not find node 'agent01.example.com'; cannot compile`, the master may be failing to find the console.

To fix this, make sure that the console is alive by [navigating to its web interface](./console_accessing.html). If it can't be reached, make sure DNS is set up correctly for the console server and ensure that the `pe-nginx` service on it is running.

If the console is alive and reachable from the master but the master can't retrieve node info from it, the master may be configured with the wrong console hostname. You'll need to:

* Edit the `reporturl` setting in the master's `/etc/puppetlabs/puppet/puppet.conf` file to point to the correct host.
* Edit the `ENC_BASE_URL` variable in the master's `/etc/puppetlabs/puppet-dashboard/external_node` file to point to the correct host. -->

### Do your agents have signed certificates?

Check the [Puppet agent logs](/pe/latest/install_what_and_where.html#puppet-agent-logs) on your nodes and look for something like the following:

    warning: peer certificate won't be verified in this SSL session

If you see this, it means the agent has submitted a certificate signing request which hasn't yet been signed. Run `puppet cert list` on the Puppet master to see a list of pending requests, then run `puppet cert sign <NODE NAME>` to sign a given node's certificate. The node should successfully retrieve and apply its configuration the next time it runs.

### Do agents trust the master's certificate?

Check the [Puppet agent logs](/pe/latest/install_what_and_where.html#puppet-agent-logs) on your nodes and look for something like the following:

    err: Could not retrieve catalog from remote server: SSL_connect returned=1 errno=0
    state=SSLv3 read server certificate B: certificate verify failed.  This is often
    because the time is out of sync on the server or client

This could be one of several things.

#### Are agents contacting the master at a valid DNS name?

When you installed the Puppet master role, you approved a list of valid DNS names to be included in the master's certificate. **Agents will ONLY trust the master if they contact it at one of THESE hostnames.**

To see the hostname agents are using to contact the master, run `puppet agent --configprint server`. If this does not return one of the valid DNS names you chose during installation of the master, edit the `server` setting in the agents' `/etc/puppetlabs/puppet/puppet.conf` files to point to a valid DNS name.

If you need to reset your Puppet master's valid DNS names, run the following:

    $ /etc/init.d/pe-nginx stop
    $ puppet cert clean <puppet master's certname>
    $ puppet cert generate <puppet master's certname> --dns_alt_names=<comma-separated list of DNS names>
    $ /etc/init.d/pe-nginx start

#### Is time in sync on your nodes?

...and was time in sync when your certificates were created?

Compare the output of `date` on your nodes. Then, run the following command on the Puppet master to check the validity dates of a given certificate:

    $ openssl x509 -text -noout -in $(puppet master --configprint ssldir)/certs/<NODE NAME>.pem

* If time is out of sync, get it in sync. Keep in mind that NTP can behave unreliably on virtual machines.
* If you have any certificates that aren't valid until the future:
    * Delete the certificate on the Puppet master with `puppet cert clean <NODE NAME>`.
    * Delete the SSL directory on the offending agent with `rm -r $(puppet agent --configprint ssldir)`.
    * Run `puppet agent --test` on that agent to generate a new certificate request, then sign that request on the master with `puppet cert sign <NODE NAME>`.

#### Did you previously have an unrelated node with the same certname?

If a node re-uses an old node's certname and the master retains the previous node's certificate, the new node will be unable to request a new certificate.

Run the following on the master:

    $ puppet cert clean <NODE NAME>

Then, run the following on the agent node:

    $ rm -r $(puppet agent --configprint ssldir)
    $ puppet agent --test

This should properly generate a new signing request.

### Can agents reach the filebucket server?

Agents attempt to back up files to the filebucket on the Puppet master, but they get the filebucket hostname from the site manifest instead of their configuration file. If Puppet agent is logging "could not back up" errors, your nodes are probably trying to back up files to the wrong hostname. These errors look like this:

    err: /Stage[main]/Pe_mcollective/File[/etc/puppetlabs/mcollective/server.cfg]/content:
    change from {md5}778087871f76ce08be02a672b1c48bdc to
    {md5}e33a27e4b9a87bb17a2bdff115c4b080 failed: Could not back up
    /etc/puppetlabs/mcollective/server.cfg: getaddrinfo: Name or service not known

This usually happens when Puppet master is installed with a certname that isn't its hostname. To fix these errors, edit `/etc/puppetlabs/puppet/manifests/site.pp` **on the Puppet master** so that the following resource's `server` attribute points to the correct hostname:

~~~ ruby
    # Define filebucket 'main':
    filebucket { 'main':
      server => '<PUPPET MASTER'S DNS NAME>',
      path   => false,
    }
~~~

Changing this on the Puppet master will fix the error on all agent nodes.


