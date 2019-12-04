# Forming requests to the status API

The HTTPS status endpoints are available on the console services API server, which uses port 4433 by default.

The path prefix is `/status`, so, for example, the URL to get the statuses for all services as JSON is `https://<DNS NAME OF YOUR CONSOLE HOST>:4433/status/v1/services`.

To access that URL using curl commands, run:

```
curl https://<DNS NAME OF YOUR CONSOLE HOST>:4433/status/v1/services \
    --cert /etc/puppetlabs/puppet/ssl/certs/<WHITELISTED CERTNAME>.pem \
    --key /etc/puppetlabs/puppet/ssl/private_keys/<WHITELISTED CERTNAME>.pem \
    --cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem
```

If enabled, the HTTP status endpoints are available on port 8123.

**Tip:** To change the port, locate the **PE Console** node group in the console, and in the **puppet\_enterprise::profile::console** class, set the **console\_services\_plaintext\_status\_port** parameter to your desired port number.

