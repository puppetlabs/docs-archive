---
layout: default
title: "Puppet Container Registry SSL"
---

This document covers using Puppet Container Registry with and without SSL.

It is important to note that the docker command line expects to communicate with a secure registry. If the docker registry does not have a valid SSL certificate, then it is considered an insecure registry.

Puppet Container Registry listens on both port 80 HTTP and port 443 HTTPS. Puppet Container Registry does not come with a valid certificate for SSL. You will have to obtain a certificate from a valid certificate authority (CA).

<h3>Using Puppet Container Registry without SSL</h3>

You can use the docker command line to push and pull from Puppet Container Registry without an SSL certificate. This will require adding the <b>--insecure-registry</b> to your local docker daemon for it to communicate with a docker registry without a valid SSL certificate. This is true for any docker registry.

On Linux, this option can be set in <code>/etc/default/docker</code>.

Realize that docker command line will attempt to reach a host on port 443 unless otherwise specified. Here are 2 examples:

For port 443
~~~
DOCKER_OPTS="--insecure-registry europa.example.com"
~~~

For port 80
~~~
DOCKER_OPTS="--insecure-registry europa.example.com:80"
~~~

You must restart the docker engine to enable this change.

When you login, push, or pull from the registry, you must use the same name as specified in the --insecure-registry option. For example:

~~~
docker login -u TOKEN -p 1234567890abc europa.example.com:80
docker build europa.example.com:80/galileo:tag009 .
docker push europa.example.com:80/galileo:tag009
docker pull europa.example.com:80/galileo:tag001
~~~


<h3>Using Puppet Container Registry with SSL</h3>

Add a SSL certificate to Puppet Container Registry with the following procedure.

<ol>
  <li>Click the <b>gear</b> on the top right.</li>
  <li>Click <b>SSL</b> under Settings.</li>
  <li>Check <b>[x] SSL Enabled</b>.</li>
  <li>Enter the <b>Server Private Key</b>.</li>
  <li>Enter the <b>Server Certificate</b>.</li>
  <li>Enter the <b>CA Certificate</b>.</li>
  <li>Scroll to the bottom and click the <b>Save SSL Settings</b> button.</li>
</ol>

New SSL settings will take effect immediately. If you change your SSL settings at a later date, you will have to restart Puppet Container Registry for the new certificate to take effect.



