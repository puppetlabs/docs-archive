# Forming activity service API requests

Web session authentication is required to access the activity service API. You can authenticate requests with user authentication tokens or whitelisted certificates.

By default, the activity service listens on port 4433. All endpoints are relative to the `/activity-api/` path. So, for example, the full URL for the `/v1/events` endpoint on localhost is `https://localhost:4433/activity-api/v1/events`.

## Authentication using tokens

Insert a user authentication token variable in an activity service API request.

1.  Generate a token: `puppet-access login`
2.  Print the token and copy it: `puppet-access show`

3.  Save the token as an environment variable: `export TOKEN=<PASTE THE TOKEN HERE>`
4.  Include the token variable in your API request:

```
curl -k -X GET https://<HOSTNAME>:<PORT>/activity-api/v1/events?service_id=classifier -H "X-Authentication:$TOKEN"
```

## Authentication using whitelisted certificate

You can also authenticate requests using a certificate listed in RBAC's certificate whitelist, located at `/etc/puppetlabs/console-services/rbac-certificate-whitelist`. Note that if you edit this file, you must reload the `pe-console-services` service \(run `sudo service pe-console-services reload`\) for your changes to take effect.

Attach the certificate using the command line, as demonstrated in the example curl query below. You must have the whitelisted certificate name \(which must match a name in the `/etc/puppetlabs/console-services/rbac-certificate-whitelist` file\) and the private key to run the script.

```
curl -X GET https://<HOSTNAME>:<PORT>/activity-api/v1/events?service_id=classifier \
		--cert /etc/puppetlabs/puppet/ssl/certs/<WHITELISTED CERTNAME>.pem \
		--key /etc/puppetlabs/puppet/ssl/private_keys/<WHITELISTED CERTNAME>.pem \
		--cacert /etc/puppetlabs/puppet/ssl/certs/ca.pem
```

You do not need to use an agent certificate for authentication. You can use `puppet cert generate` to create a new certificate specifically for use with the API.

**Related information**  


[Token-based authentication](rbac_token_auth_intro.md#)

