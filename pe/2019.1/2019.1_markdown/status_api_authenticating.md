# Authenticating to the status API

Token-based authentication is not required to access the status API. You can choose to authenticate requests by using whitelisted certificates, or can access the API without authentication via HTTP.

You can authenticate requests using a certificate listed in RBAC's certificate whitelist, located at `/etc/puppetlabs/console-services/rbac-certificate-whitelist`. Note that if you edit this file, you must reload the `pe-console-services` service \(run `sudo service pe-console-services reload`\) for your changes to take effect.

The status API's endpoints can be served over HTTP, which does not require any authentication. This is disabled by default.

**Tip:** To use HTTP, locate the **PE Console** node group in the console, and in the **puppet\_enterprise::profile::console** class, set **console\_services\_plaintext\_status\_enabled** to **true**.

