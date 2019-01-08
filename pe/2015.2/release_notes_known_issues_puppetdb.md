---
title: "PE 2015.2 » Release Notes >> Known Issues"
layout: default
subtitle: "PuppetDB and PostgreSQL Known Issues"
canonical: "/pe/latest/release_notes_known_issues_puppetdb.html"
---

As we discover them, this page will be updated with known issues related to PuppetDB and PostgreSQL in Puppet Enterprise 2015.2 releases. Fixed issues will be removed from this page and noted in the Bug Fixes section of the release notes. If you find new problems yourself, please file bugs in [our issue tracker](https://tickets.puppetlabs.com).

## Incorrect Password for Database User(s) Causes Install/Upgrade to Fail

If, during installation or upgrade, you supply an incorrect password for one of the PE databases users (RBAC, console, PuppetDB), the install/upgrade will fail. However, in some cases it might appear that the install/upgrade was successful. For example, if the incorrect password is supplied for the console database user, the install/upgrade continues---and appears to succeed---but the console will not be functional.

## Errors Related to Stopping `pe-postgresql` Service

If for any reason the `pe-postgresql` service is stopped, agents receive several different error messages, for example:

    Warning: Unable to fetch my node definition, but the agent run will continue:
    Warning: Error 400 on SERVER: (<unknown>): mapping values are not allowed in this context at line 7 column 28

or, when attempting to request a catalog:

    Error: Could not retrieve catalog from remote server: Error 400 on SERVER: (<unknown>): mapping values are not allowed in this context at line 7 column 28
    Warning: Not using cache on failed catalog
    Error: Could not retrieve catalog; skipping run

If you encounter these errors, simply re-start the `pe-postgresql` service.

## PuppetDB Behind a Load Balancer Causes Puppet Server Errors

Puppet Server handles outgoing HTTPS connections differently from the older MRI Ruby Puppet master, and has a new restriction on the server certificates it will accept. This affects all Puppet Enterprise versions in the 2015.2.x series.

If Puppet Server makes client connections to another HTTPS service, that service must use only one certificate. If that service is behind a load balancer, and the back-end servers use individual certificates, Puppet Server will frequently abort its client connections. For more details, see [this note from the Puppet Server docs.](/puppetserver/1.0/ssl_server_certificate_change_and_virtual_ips.html)

Therefore, if you are running multiple PuppetDB servers behind a load balancer, you must configure all of them to use the same certificate. You can do this by following the instructions below.

> **Warning**: This is a non-standard configuration that may or may not be supported, depending on what your organization has negotiated with Puppet Labs.
>
> Also note that if you use this workaround, you will not be able to use `/opt/puppetlabs/sbin/puppetdb ssl-setup` to repair certificate issues with PuppetDB.

1. On the Puppet master that serves as the CA, generate a certificate for your PuppetDB nodes with the load balancer hostname as one of the DNS alt names. In this example, we use `pe-internal-puppetdb`.

   `puppet cert generate <pe-internal-puppetdb> --dns_alt_names=<LOAD BALANCER HOSTNAME>`

2. Move the new cert from the Puppet master SSL cert directory (`/etc/puppetlabs/puppet/ssl/certs/pe-internal-puppetdb.pem`) to the same path on each PuppetDB node (`/etc/puppetlabs/puppet/ssl/certs/pe-internal-puppetdb.pem`).
3. Move the new private key from the Puppet master SSL private key directory (`/etc/puppetlabs/puppet/ssl/private_keys/pe-internal-puppetdb.pem`) to the same path on each PuppetDB node (`/etc/puppetlabs/puppet/ssl/private_keys/pe-internal-puppetdb.pem`).
4. Move the new public key from Puppet master SSL public key directory (`/etc/puppetlabs/puppet/ssl/public_keys/pe-internal-puppetdb.pem`) to the same path on each PuppetDB node (`/etc/puppetlabs/puppet/ssl/public_keys/pe-internal-puppetdb.pem`).
5. In the PE console, configure the `puppet_enterprise::profile::puppetdb` class to use the new `pe-internal-puppetdb` certname.

   a. Click **Classification** in the top navigation bar.

   b. From the **Classification** page, select the **PE PuppetDB** group.

   c. Click the **Classes** tab, and find the `puppet_enterprise::profile::puppetdb` class.

   d. From the **Parameter** drop-down list, select **`certname`**.

   e. In the **Value** field, enter `pe-internal-puppetdb`.

   f. Click **Add parameter** and the **Commit change** button.

6. Add the hostname of the load balancer to the **PE Infrastructure** class.

   a. Navigate to the **PE Infrastructure** group.

   b. Click the **Classes** tab, and find the `puppet_enterprise` class.

   c. For the **`puppetdb_host`** entry, edit the value to reflect the hostname of your load balancer.

   d. Click the **Commit change** button.

7. On each PuppetDB node and the Puppet master, kick off a Puppet run.