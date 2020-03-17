# Configure RBAC and token-based authentication settings

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


**Parent topic:**[Configuring and tuning security settings](configuring_security_settings.md)

