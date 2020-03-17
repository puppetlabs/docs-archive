# Configure cipher suites

Due to regulatory compliance or other security requirements, you may need to change which cipher suites your SSL-enabled PE services use to communicate with other PE components.

## SSL ciphers for core Puppet services

To add or remove cipher suites for [core Puppet services](pe_architecture_overview.md#), use Hiera to add an array of SSL ciphers to the `puppet_enterprise::ssl_cipher_suites` parameter.

**Note:** Changing this parameter overrides the default list of SSL cipher suites.

The example Hiera data below replaces the default list of cipher suites to only allow the four specified.

```
puppet_enterprise::ssl_cipher_suites:
- 'SSL_RSA_WITH_NULL_MD5'
- 'SSL_RSA_WITH_NULL_SHA'
- 'TLS_DH_anon_WITH_AES_128_CBC_SHA'
- 'TLS_DH_anon_WITH_AES_128_CBC_SHA256'
```

**Note:** Cipher names are in [IANA RFC naming format](https://www.iana.org/assignments/tls-parameters/tls-parameters.xhtml#tls-parameters-4).

## SSL for console services

To add or remove cipher suites for console services affecting traffic on port 443, use Hiera or the console to change the `puppet_enterprise::profile::console::proxy::ssl_ciphers` parameter.

For example, to change the parameter in the console, in the **PE Console** node group, add an array of SSL ciphers to the `ssl_ciphers` parameter in the `puppet_enterprise::profile::console::proxy` class.

**Parent topic:**[Configuring and tuning security settings](configuring_security_settings.md)

