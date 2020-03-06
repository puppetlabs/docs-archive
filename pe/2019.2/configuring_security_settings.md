# Configuring and tuning security settings

Ensure your PE environment is secure by configuring security settings.

## Configure cipher suites

Due to regulatory compliance or other security requirements, you may need to change which cipher suites your SSL-enabled PE services use to communicate with other PE components.

### SSL ciphers for core Puppet services

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

### SSL for console services

To add or remove cipher suites for console services affecting traffic on port 443, use Hiera or the console to change the `puppet_enterprise::profile::console::proxy::ssl_ciphers` parameter.

For example, to change the parameter in the console, in the **PE Console** node group, add an array of SSL ciphers to the `ssl_ciphers` parameter in the `puppet_enterprise::profile::console::proxy` class.

## Configure SSL protocols

Add or remove SSL protocols in your PE infrastructure.

To change what SSL protocols your PE infrastructure uses, use Hiera or the console to add or remove protocols.

Use the parameter `puppet_enterprise::master::puppetserver::ssl_protocols` and add an array for protocols you want to include, or remove protocols you no longer want to use.

For example, to enable TLSv1.1 and TLSv1.2, set the following parameter in the **PE Infrastructure** group in the console or in your Hiera data.

```
puppet_enterprise::master::puppetserver::ssl_protocols["TLSv1.1", "TLSv1.2"]
```

**Note:** To comply with security regulations, PE 2019.1 and later uses only version 1.2 of the Transport Layer Security \(TLS\) protocol.

## Configure RBAC and token-based authentication settings

Tune RBAC and token-based authentication settings, like setting the number of failed attempts a user has before they are locked out of the console or changing the amount of time a token is valid for.

RBAC and token authentication settings can be changed in the **PE Infrastructure** group in the console or in your Hiera data. Below is a list of related settings.

-   **`puppet_enterprise::profile::console::rbac_failed_attempts_lockout`**

    An integer specifying how many failed login attempts are allowed on an account before the account is revoked. The default is "10" \(attempts\).

-   **`puppet_enterprise::profile::console::rbac_password_reset_expiration`**

    An integer representing, in hours, how long a user's generated token is valid for. An administrator generates this token for a user to that they can reset their password. The default is "24" \(hours\).

-   **`puppet_enterprise::profile::console::rbac_session_timeout`**

    Integer representing, in minutes, how long a user's session can last. The session length is the same for node classification, RBAC, and the console. The default is "60" \(minutes\).

-   **`puppet_enterprise::profile::console::rbac_token_auth_lifetime`**

    A value representing the default authentication lifetime for a token. It cannot exceed the `rbac_token_maximum_lifetime`. This is represented as a numeric value followed by "y" \(years\), "d" \(days\), "h" \(hours\), "m" \(minutes\), or "s" \(seconds\). The default is "5m".

-   **`puppet_enterprise::profile::console::rbac_token_maximum_lifetime`**

    A value representing the maximum allowable lifetime for all tokens. This is represented as a numeric value followed by "y" \(years\), "d" \(days\), "h" \(hours\), "m" \(minutes\), or "s" \(seconds\). The default is "10y".

-   **`puppet_enterprise::profile::console::rbac_account_expiry_check_minutes`**

    An integer that specifies, in minutes, how often the application checks for idle user accounts. The default value is "60" \(minutes\).

-   **`puppet_enterprise::profile::console::rbac_account_expiry_days`**

    An integer that specifies, in days, the duration before an inactive user account expires. The default is undefined. To activate the feature, add a value of "1" or greater.

    If a non-superuser hasn't logged into the console during this specified period, their user status updates to revoked. After creating an account, if a non-superuser hasn't logged in to the console during the specified period, their user status updates to revoked.

    **Note:** If you do not specify the `rbac_account_expiry_days` parameter, the `rbac_account_expiry_check_minutes` parameter is ignored.


