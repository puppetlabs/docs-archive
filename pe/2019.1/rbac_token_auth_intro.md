# Token-based authentication

Puppet Enterprise users generate tokens to authenticate their access to certain PE command-line tools and API endpoints. Authentication tokens are tied to the permissions granted to the user through RBAC, and provide the user with the appropriate access to HTTP requests.

Authentication tokens manage access to the following PE services:

-   Puppet orchestrator
-   Code Manager
-   Node classifier
-   Role-based access control \(RBAC\)
-   Activity service
-   PuppetDB

You can generate tokens with the `puppet-access` command or with the API endpoint.

**Remember:** For security reasons, authentication tokens can be generated only for revocable users. The admin user and api\_user cannot be revoked.

**Related information**  


[Installing PE client tools](installing_pe_client_tools.md#)

[User permissions](rbac_permissions_intro.md#)

## Generate a token using puppet-access

Use `puppet-access` to generate an authentication tokens from the command line of any workstation \(Puppet-managed or not\), without the need to SSH into the Puppet master.

### Before you begin

Install the PE client tools package and configure `puppet-access`.

### About this task

### Procedure

1.  Choose one of the following options, depending on how long you need your token to last:

    -   To generate a token with the default five-minute lifetime, run `puppet-access login`.

    -   To generate a token with a specific lifetime, run `puppet-access login --lifetime <TIME PERIOD>`.

        For example, `puppet-access login --lifetime 5h` generates a token that lasts five hours.

2.  When prompted, enter the same username and password that you use to log into the PE console.

    The `puppet-access` command contacts the token endpoint in the RBAC v1 API. If your login credentials are correct, the RBAC service generates a token.

3.  The token is generated and stored in a file for later use. The default location for storing the token is `~/.puppetlabs/token`. You can print the token at any time using `puppet-access show`.


### Results

You can continue to use this token until it expires, or until your access is revoked. The token has the exact same set of permissions as the user that generated it.

CAUTION:

If you run the login command with the `--debug` flag, the client outputs the token, as well as the username and password. For security reasons, exercise caution when using the `--debug` flag with the login command.

**Related information**  


[Setting a token-specific lifetime](rbac_token_auth_intro.md#)

### Generate a token for use by a service

If you need to generate a token for use by a service and the token doesn't need to be saved, use the `--print` option.

#### Before you begin

Install the PE client tools package and configure `puppet-access`.

#### Procedure

1.  Run `puppet-access login [username] --print`.

    This command generates a token, and then displays the token content as stdout \(standard output\) rather than saving it to disk.


#### Results

**Tip:** When generating a token for a service, consider specifying a longer token lifetime so that you don't have to regenerate the token too frequently.

### Configuring puppet-access

The `puppet-access` command allows users to generate and manage authentication tokens from the command line of any workstation \(Puppet-managed or not\), without the need to SSH into the Puppet master.

The configuration file for `puppet-access` allows you to generate tokens from the CLI without having to pass additional flags.

Whether you are running `puppet-access` on a PE-managed server or installing it on a separate work station, you need a global configuration file and a user-specified configuration file.

#### Global configuration file

The global configuration file is located at:

-   **On \*nix systems:** `/etc/puppetlabs/client-tools/puppet-access.conf`

-   **On Windows systems:** `C:/ProgramData/PuppetLabs/client-tools/puppet-access.conf`

On machines managed by Puppet Enterprise, this global configuration file is created for you. The configuration file is formatted in JSON. For example:

```JSON
{
    "service-url": "https://<CONSOLE HOSTNAME>:4433/rbac-api",
    "token-file": "~/.puppetlabs/token",
    "certificate-file": "/etc/puppetlabs/puppet/ssl/certs/ca.pem"
}
```

PE determines and adds the `service-url` setting.

If you're running `puppet-access` from a workstation not managed by PE, you must create the global file and populate it with the configuration file settings.

#### User-specified configuration file

The user-specified configuration file is located at `~/.puppetlabs/client-tools/puppet-access.conf` for both \*nix and Windows systems. You must create the user-specified file and populate it with the configuration file settings. A list of configuration file settings is found in the next section.

The user-specified configuration file always takes precedence over the global configuration file. For example, if the two files have contradictory settings for the `token-file`, the user-specified settings prevail.

**Important:** User-specified configuration files must be in JSON format; HOCON and INI-style formatting are not supported.

### Configuration file settings for puppet-access

As needed, you can manually add configuration settings to your user-specified or global `puppet-access` configuration file.

The class that manages the global configuration file is `puppet_enterprise::profile::controller`.

You can also change configuration settings by specifying flags when using the `puppet-access` command in the command line.

|Setting|Description|Command line flag|
|-------|-----------|-----------------|
|`token-file`|The location for storing authentication tokens. Defaults to `~/.puppetlabs/token`.|`-t`, `--token-file`|
|`certificate-file`|The location of the CA that signed the console-services server's certificate. Defaults to the PE CA cert location, `/etc/puppetlabs/puppet/ssl/certs/ca.pem`.|`--ca-cert`|
|`config-file`|Changes the location of your configuration file. Defaults to `~/.puppetlabs/client-tools/puppet-access.conf`.|`-c`, `--config-file`|
|`service-url`|The URL for your RBAC API. Defaults to the URL automatically determined during the client tools package installation process, generally `https://<CONSOLE HOSTNAME>:4433/rbac-api`. You typically need to change this only if you are moving the console server.|`--service-url`|

## Generate a token using the API endpoint

The RBAC v1 API includes a token endpoint, which allows you to generate a token using curl commands.

### About this task

### Procedure

1.  On the command line, post your RBAC user login credentials using the token endpoint.

    ```
    curl -k -X POST -H 'Content-Type: application/json' -d '{"login": "<YOUR PE USER NAME>", "password": "<YOUR PE PASSWORD>"}' https://$<HOSTNAME>:4433/rbac-api/v1/auth/token
    ```

    The command returns a JSON object containing the key `token` and the token itself.

    CAUTION:

    If you are using curl commands with the `-k` insecure SSL connection option, keep in mind that you are vulnerable to a person-in-the-middle attack.

2.  Save the token. Depending on your workflow, either:

    -   Copy the token to a text file.

    -   Save the token as an environment variable using `export TOKEN=<PASTE THE TOKEN HERE>`.


### Results

You can continue to use this token until it expires, or until your access is revoked. The token has the exact same set of permissions as the user that generated it.

## Use a token with the PE API endpoints

The example below shows how to use a token in an API request. In this example, you use the `/users/current` endpoint of the RBAC v1 API to get information about the current authenticated user.

### Before you begin

Generate a token and save it as an environment variable using `export TOKEN=<PASTE THE TOKEN HERE>`.

### Procedure

1.  Run the following command: `curl -k -X GET https://<HOSTNAME>:4433/rbac-api/v1/users/current -H "X-Authentication:$TOKEN"`

    The command above uses the X-Authentication header to supply the token information.

    In some cases, such as GitHub webhooks, you might need to supply the token in a token parameter by specifying the request as follows: `curl -k -X GET https://<HOSTNAME>:4433/rbac-api/v1/users/current?token=$TOKEN`

    CAUTION:

    If you are using curl commands with the `-k` insecure SSL connection option, keep in mind that you are vulnerable to a person-in-the-middle attack.


## Change the token's default lifetime

Tokens have a default authentication lifetime of five minutes, but this default value can be adjusted in the console. You can also change a token's maximum authentication lifetime, which defaults to 10 years.

### About this task

### Procedure

1.  In the console, click Classification.

2.  Open the **PE Infrastructure** node group and click the **PE Console** node group.

3.  On the **Configuration** tab, find the **puppet\_enterprise::profile::console** class.

4.  In the **Parameter** field, select **rbac\_token\_auth\_lifetime** to change the default lifetime of all tokens, or **rbac\_token\_maximum\_lifetime** to adjust the maximum allowable lifetime for all tokens.

5.  In the **Value** field, enter the new default authentication lifetime.

    Specify a numeric value followed by "y" \(years\), "d" \(days\), "h" \(hours\), "m" \(minutes\), or "s" \(seconds\). For example, "12h" generates a token valid for 12 hours.

    Do not add a space between the numeric value and the unit of measurement. If you do not specify a unit, it is assumed to be seconds.

    The **rbac\_token\_auth\_lifetime** cannot exceed the **rbac\_token\_maximum\_lifetime** value.

6.  Click **Add parameter**, and commit changes.


## Setting a token-specific lifetime

Tokens have a default lifetime of five minutes, but you can set a different lifetime for your token when you generate it. This allows you to keep one token for multiple sessions.

Specify a numeric value followed by "y" \(years\), "d" \(days\), "h" \(hours\), "m" \(minutes\), or "s" \(seconds\). For example, "12h" generates a token valid for 12 hours.

Do not add a space between the numeric value and the unit of measurement. If you do not specify a unit, it is assumed to be seconds.

Use the `--lifetime` parameter if using `puppet-access` to generate your token. For example: `puppet-access login --lifetime 1h`.

Use the `lifetime` value if using the RBAC v1 API to generate your token. For example: `{"login": "<YOUR PE USER NAME>", "password": "<YOUR PE PASSWORD>", "lifetime": "1h"}`.

## Set a token-specific label

You can affix a plain-text, user-specific label to tokens you generate with the RBAC v1 API. Token labels allow you to more readily refer to your token when working with RBAC API endpoints, or when revoking your own token.

### About this task

Token labels are assigned on a per-user basis: two users can each have a token labelled “my token”, but a single user cannot have two tokens both labelled “my token.” You cannot use labels to refer to other users’ tokens.

### Procedure

1.  Generate a token using the `token` endpoint of the RBAC API, using the `label` value to specify the name of your token.

    `{"login": "<YOUR PE USER NAME>", "password": "<YOUR PE PASSWORD>", "label": "Ava's token"}`

    Labels must be no longer than 200 characters, must not contain commas, and must contain something besides whitespace.


## Revoking a token

Revoke tokens by username, label, or full token through the `token` endpoint of the v2 RBAC API. All token revocation attempts are logged in the activity service, and can be viewed on the user's **Activity** tab in the console.

You can revoke your own token by username, label, or full token. You can also revoke any other full token you possess. Users with the permission to revoke other users can also revoke those users' tokens, as the `users:disable` permission includes token revocation. Revoking a user's tokens does not revoke the user's PE account.

If a user's account is revoked, all tokens associated with that user account are also automatically revoked.

**Note:** If a remote user generates a token and then is deleted from your external directory service, the deleted user cannot log into the console. However, since the token has already been authenticated, the RBAC service does not contact the external directory service again when the token is used in the future. To fully remove the token's access, you need to manually revoke or delete the user from PE.

## Delete a token file

If you logged into `puppet-access` to generate a token, you can remove the file that stores that token simply by running the `delete-token-file` command. This is useful if you are working on a server that is used by other people.

### About this task

Deleting the token file prevents other users from using your authentication token, but does not actually revoke the token. Once the token has expired, there's no risk of obtaining the contents of the token file.

### Procedure

1.  From the command line, run one of the following commands, depending on the path to your token file:

    -   If your token is at the default token file location, run `puppet-access delete-token-file`.

    -   If you used a different path to store your token file, run `puppet-access delete-token-file --token-path <YOUR TOKEN PATH>`.


## View token activity

Token activity is logged by the activity service. You can see recent token activity on any user's account in the console.

### About this task

### Procedure

1.  In the console, click Access control \> Users. Click the full name of the user you are interested in.

2.  Click the **Activity** tab.


