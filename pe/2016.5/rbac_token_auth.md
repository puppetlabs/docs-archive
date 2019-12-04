---
layout: default
title: "Token-based authentication"
canonical: "/pe/latest/rbac_token_auth.html"
---

## When are tokens used?

Puppet Enterprise users generate tokens to authenticate their access to certain PE command-line tools and API endpoints. Use authentication tokens to manage access to the following PE services:

* [Puppet orchestrator](./orchestrator_install.html#setting-pe-rbac-permissions-and-token-authentication-for-puppet-orchestrator)
* [Code Manager](./code_mgr_config.html#request-an-authentication-token)
* [Node classifier](./nc_forming_requests.html#authentication-token)
* [Role-based access control (RBAC)](./rbac_forming_requests_v1.html#authentication-token)
* [Activity service](./rbac_activityapis.html#authentication-token)

Authentication tokens are tied to the [permissions granted to the user through RBAC](./rbac_permissions.html), and provide the user with the appropriate access to HTTP requests.

> **Note:** For security reasons, authentication tokens can only be generated for revocable users. The admin user and api_user cannot be revoked.

### Benefits of using tokens for authentication

* Tokens allow you to take action remotely, from your workstation for example, without having to log into PE-managed servers.
* Tokens are RBAC-aware, meaning that they reflect the user permissions granted through RBAC.
* Tokens are issued per user. Their usage is logged on a per-user basis.
* Tokens have a limited lifetime.

You can generate tokens with the [`puppet-access` command](./rbac_token_auth.html#working-with-puppet-access) or with the [API endpoint](./rbac_token_auth.html#generating-tokens-with-the-api-endpoint).

## Working with `puppet-access`

The `puppet-access` command allows users to generate and manage authentication tokens from the command line of any workstation (Puppet-managed or not), without the need to SSH into the Puppet master.

### Installing and configuring `puppet-access`

For information on installing the PE client tools package, which includes the `puppet-access` command, see [installing the PE client tools package](./install_pe_client_tools.html).

The configuration file for `puppet-access` allows you to generate tokens from the CLI without having to pass additional flags.

Whether you are running `puppet-access` on a PE-managed server or installing it on a separate work station, there are two places where you can have a configuration file:

- A **global configuration file** at:

   * **nix systems** --- `/etc/puppetlabs/client-tools/puppet-access.conf`
   * **Windows** --- `C:/ProgramData/PuppetLabs/client-tools/puppet-access.conf`

   This global configuration file is created by Puppet Enterprise for PE-managed machines only. PE will add the **service-url** setting.

   If you're running `puppet-access` from a non-managed workstation, you will need to create the global file and populate it with the [configuration file settings](./rbac_token_auth.html#configuration-file-settings-for-puppet-access).

- A **user-specified configuration file** at `~/.puppetlabs/puppet-access.conf` (for both *nix and Windows), which you will need to create and populate with the [configuration file settings](./rbac_token_auth.html#configuration-file-settings-for-puppet-access).

> **Note:** User-created configuration files must be in JSON format; HOCON and INI-style formatting are not supported.

The user-specified configuration always takes precedence over the global configuration file. For example, if the two files have contradictory settings for the `token-file`, the user-specified settings will prevail.

The config file is formatted in JSON. For example:

~~~JSON
{
    "service-url": "https://<CONSOLE HOSTNAME>:4433/rbac-api",
    "token-file": "~/.puppetlabs/token",
    "certificate-file": "/etc/puppetlabs/puppet/ssl/certs/ca.pem"
}
~~~

### Configuration file settings for `puppet-access`

The class that manages the global configuration file is `puppet_enterprise::profile::controller`. As needed, you can manually add the following settings to your user-specified or global `puppet-access` configuration file:

- `token-file`: the default location for storing authentication tokens. Defaults to `~/.puppetlabs/token`.

   Use `-t`, `--token-file` if setting this from the command line.

- `certificate-file`: the location of the CA that signed the console-services server's certificate. Defaults to the PE CA cert location, `/etc/puppetlabs/puppet/ssl/certs/ca.pem`.

   Use `--ca-cert` if setting this from the command line.

- `config-file`: changes the location of your configuration file (Default: `~/.puppetlabs/puppet-access.conf`)

   Use `-c`, `--config-file` if setting this from the command line.

- `service-url`: the URL for your RBAC API. Defaults to the URL automatically determined by PE during the client tools package installation process, generally `https://<CONSOLE HOSTNAME>:4433/rbac-api`.

### Generating a token using `puppet-access`

To generate an authentication token using `puppet-access`:

1. Choose one of the following options:

   - To generate a token with the default five minute lifetime, run `puppet-access login`.

   - To generate a token with a [specific lifetime](#setting-a-token-specific-lifetime), run `puppet-access login --lifetime <TIME PERIOD>`.

     For example, `puppet-access login --lifetime 5h` will generate a token that lasts five hours.

2. When prompted, enter the same username and password that you use to log into the PE console.

To generate your token, the `puppet-access` command contacts the [token endpoint in the RBAC v1 API](./rbac_token_v1.html). If your login credentials are correct, the RBAC service generates a token.

The token is stored in a file for later use. The default location for storing the token is `~/.puppetlabs/token`.

> **Notes about token generation**
>
>- If you run the login command with the `--debug` flag, the client outputs the token, as well as the username and password. For security reasons, exercise caution when using the `--debug` flag with the login command.
>
>- You can print the token at any time using `puppet-access show`.

### Generating a token for use by a service

If you need to generate a token for use by a service and the token doesn't need to be saved, use the `--print` option. `puppet-access login [username] --print` generates a token, and then displays the token content as stdout (standard output) rather than saving it to disk.

When generating a token for a service, you may want to specify a longer token lifetime so that you don't have to regenerate the token too frequently. See [Setting a token-specific lifetime](./rbac_token_auth.html#setting-a-token-specific-lifetime) for more information.

### Changing `puppet-access` configuration settings

There are several options that you can specify with the `puppet-access` command in the command line.

* `--service-url`: Changes the contact information for the server issuing the token. The token is issued by the RBAC service. You typically only need to change this if you are moving the PE console server.

  (Example: `puppet-access --service-url https://puppetmaster.example.com:4433/rbac-api`)

* `--ca-cert`: Sets the path to the CA certificate file to use for the requests to the RBAC API.

* `-c`, `--config-file`: Changes the location of your configuration file.

  (Default: ~/.puppetlabs/puppet-access.conf)

* `-t`, `--token-file`: Changes the location of the file that the token is stored in.

  (Default: ~/.puppetlabs/token)

## Generating tokens with the API endpoint

The RBAC v1 API includes a [token endpoint](./rbac_token_v1.html).

To generate a token using curl commands:

1. On the command line, post your RBAC user login credentials using the token endpoint:

   ```
   `curl -k -X POST -H 'Content-Type: application/json' -d '{"login": "<YOUR PE USER NAME>", "password": "<YOUR PE PASSWORD>"}' https://$<HOSTNAME>:4433/rbac-api/v1/auth/token`
   ```

   See the [token endpoint docs](./rbac_token_v1.html#example-requests) for a full explanation of the parts of this curl command request.

2. Save the returned token.

   The returned token is a JSON object containing the key `token` and the token itself. 
   ```
 {"token":"eyJhbGciOi-JSUzUxM_iIsInR5cCI6IkpXVCJ9eyJpc3"}
   ```

   Copy the returned token to a text file or environment variable. For example, you can save it as an environment variable using `export TOKEN=<PASTE THE TOKEN HERE>`.

## Using a token

When you generate a token, you can continue to use it until it expires, or until your access is revoked. The token has the exact same set of permissions as the user that generated it.

### Using a token with PE API endpoints

The example below shows how to use a token in an API request. In this example, we are using the `/users/current` endpoint of the RBAC API to get information about the current authenticated user. The example assumes that you saved your token as an environment variable using `export TOKEN=<PASTE THE TOKEN HERE>`.

```
`curl -k -x GET https://<HOSTNAME>:4433/rbac-api/v1/users/current -H "X-Authentication:$TOKEN"`
```

The example above uses the X-Authentication header to supply the token information. In some cases, such as GitHub webhooks, you may need to supply the token in a token parameter by specifing the request as follows:

```
`curl -k -X GET https://<HOSTNAME>:4433/rbac-api/v1/users/current?token=$TOKEN`
```

## Viewing token activity

Token activity is logged by the PE activity service.

To view token generation activity in the PE console, click **Access control** > **Users**, and click on the user that you are interested in. In the user details screen, click the **Activity** tab.

## Managing tokens

### Changing the default lifetime

Tokens have a default authentication lifetime of five minutes, but you can also specify a token's maximum authentication lifetime, which defaults to 10 years.

1. In the PE console, click **Nodes** > **Classification**, and in the **PE Infrastructure** node group, select the **PE Console** node group.

2. On the classes tab, find the `puppet_enterprise::profile::console` class and specify parameters:

   - **Parameter** -- Select the `rbac_token_auth_lifetime` parameter or the `rbac_token_maximum_lifetime` parameter.

   - **Value** -- Enter the authentication lifetime or maximum lifetime value. You can specify a numeric value followed by "y" (years), "d" (days), "h" (hours), "m" (minutes), or "s"(seconds), but the `rbac_token_auth_lifetime` cannot exceed the `rbac_token_maximum_lifetime` value. Do not add a space between the numeric value and the unit of measurement. If you do not specify a unit, it is assumed to be seconds. 
   
     For example, a value of "12h" generates a token valid for 12 hours. 

### Setting a token-specific lifetime

All users can change the lifetime of a token. 

**When generating a token using the `puppet-access` command:**

When generating a token using `puppet-access`, use the `lifetime` option to specify the amount of time for which the token is valid. For example, `puppet-access login --lifetime 1h`.

**When generating a token using the API endpoint:**

When generating a token using [the `token` endpoint of the RBAC API](./rbac_token_v1.html), use the `lifetime` value to specify the amount of time for which the token is valid. For example, `{"login": "<YOUR PE USER NAME>", "password": "<YOUR PE PASSWORD>", "lifetime": "1h"}`.

>**Note:** With the exception of the UUID, the user information in a token is subject to change. If user information changes in your user directory, the token will become invalid.

### Setting a token-specific label

You can affix a plain-text, user-specific label to the token you generate with the RBAC v1 API. Token labels allow you to more readily refer to your token when working with RBAC API endpoints, or when revoking your own token.

Token labels are assigned on a per-user basis: two users can each have a token labelled “my token”, but a single user cannot have two tokens both labelled “my token”. You cannot use labels to refer to other users’ tokens.

When generating a token using [the `token` endpoint of the RBAC API](./rbac_token_v1.html), use the `label` value to specify the name of your token. For example, `{"login": "<YOUR PE USER NAME>", "password": "<YOUR PE PASSWORD>", "label": "Ava's token"}`.

Labels must be no longer than 200 characters, must not contain commas, and must contain something besides whitespace.

### Deleting a token file

If you logged into `puppet-access` to generate a token, you can remove the file that stores that token simply by running the `delete-token-file` command. This is useful if you are working on a server that is used by other people. Removing the token file prevents other users from using your authentication token, but it does not actually revoke the token. Once the token has expired, there's no risk of obtaining the contents of the token file.

To delete your token file, from the command line, type `puppet-access delete-token-file`.

If you do not specify a path, the tool looks for the token file at the default path. If you used a different path to store your token file, specify `--token-path` as follows:

```
`puppet-access delete-token-file --token-path <YOUR TOKEN PATH>`
```

### Revoking a token

Revoke tokens by username, label, or full token through [the `token` endpoint of the v2 RBAC API](./rbac_revoke_token_v2.html). All token revocation attempts are logged in the activity service, and may be viewed on the user's **Activity** tab in the console.

You can revoke your own token by username, label, or full token, and may also revoke any other full token you possess. Users with the [permission to revoke other users](./rbac_permissions.html) can also revoke those users' tokens, as the `users:disable` permission includes token revocation. Revoking a user's token(s) does not revoke the user's PE account.

If a [user's account is revoked](./rbac_user_roles.html#revoke-a-users-access), all tokens associated with that user account are also automatically revoked.

## Security best practices for using token authentication

1. Store credentials with care

	* Restrict access to files that store tokens or certificates. Make sure only people who should be using those credentials can read the files.

	* Don’t save your password, especially not in a script.

2. Don't accidentally expose credentials

	* If you use query parameters for your tokens (when using Code Manager, for example), unencrypted passwords can get logged by various tools.

	* When using `puppet-access`, your password is output when you log in using the debug flag.

3. Using cURL -k

	If you are using curl commands with the `-k` insecure SSL connection option, keep in mind that you are vulnerable to a person-in-the-middle attack.

4. Revoke or remove external directory users

	If a remote user generates a token and then is deleted from your external directory service, the deleted user cannot log into the console. However, since the token has already been authenticated, the RBAC service does not contact the external directory service again when the token is used in the future. To fully remove the token's access, you need to manually revoke or delete the user from PE.
