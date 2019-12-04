# Triggering Code Manager with a webhook

To deploy your code, you can trigger Code Manager by hitting a web endpoint, either through a webhook or a custom script. The webhook is the simplest way to trigger Code Manager.

Custom scripts are a good alternative if you have requirements such as existing continuous integration systems, privately hosted Git repos, or custom notifications. For information about writing a script to trigger Code Manager, see the related topic about creating custom scripts.

Code Manager supports webhooks for GitHub, Bitbucket Server \(formerly Stash\), Bitbucket, and Team Foundation Server. The webhook must only be used by the control repository. It can't be used by any other repository \(for example, other internal component module repositories\).

## Creating a Code Manager webhook

To set up the webhook to trigger environment deployments, you'll need to create a custom URL, and then set up the webhook with your Git host.

### Creating a custom URL for the Code Manager webhook

To trigger deployments with a webhook, you’ll need a custom URL to enable communication between your Git host and Code Manager.

Code Manager supports webhooks for GitHub, Bitbucket Server \(formerly Stash\), Bitbucket, GitLab \(Push events only\), and Team Foundation Server \(TFS\). To use the GitHub webhook with the Puppet signed cert, disable SSL verification.

**Note to GitLab users:** Prior to version 8.5.0, GitLab had a character limit on webhooks and would not accept the entire authentication token. If you are using a GitLab version earlier than 8.5.0, either upgrade to the latest version or turn off Code Manager webhook authentication via Hiera with the `authenticate_webhook` parameter.

To create the custom URL for your webhook, use the following elements:

-   The name of the Puppet master server \(for example, `code-manager.example.com`\).
-   The Code Manager port \(for example, 8170\).
-   The endpoint \(`/code-manager/v1/webhook/`\).
-   Any relevant query parameters \(for example, `type=github`\).
-   The complete authentication token you generated earlier \(`token=<TOKEN>`\), passed with the `token` query parameter.

For example, the URL for a GitHub webhook might look like this:

```
https://code-manager.example.com:8170/code-manager/v1/webhook?type=github&token=<TOKEN>
```

The URL for a Stash webhook might look something like this:

```
https://code-manager.example.com:8170/code-manager/v1/webhook?type=stash&prefix=dev&token=<TOKEN>
```

With the complete token attached, a GitHub URL looks something like this:

```
https://code-manager.example.com:8170/code-manager/v1/webhook?type=github&token=eyJhbGciOiJSUzUxMiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJkZXBsb3kiLCJpYXQiOjE0NDY2NjQ1OTAsImV4cCI6MTQ3ODI4Njk5MCwic3ViIjp7ImlkIjoiZWNmOTJlYzYtMjg1Zi00NzExLWI0MDktMzFmMGYzMGIzOGUwIiwibG9naW4iOiJkZXBsb3kifX0.OdEIA-HGRC0r2J87Pj2sDsyz-EMK-7sZalBTswy2e3uSv1Z6ulXaIQQd69PQnSSBExQotExDgXZInyQa_le2gwTo4smjUaBd6_lnPYr6GJ4hjB4-fT8dNnVuvZaE5WPkKt-sNJKJwE9LiEd4W42aCYfse1KNgPuXR5m77SRsUy86ymthVPKHqqexEyuS7LGeQJvyQE1qEejSdbiLg6znlJXhglWic6ZEpHcYBrFFE4aNwuOYXM4yIL803yIMviBwXRtt9HxbtUBZ8D4ag14ELMefQTlqrKvepKXd29oJG06FwkKcWV9PWiQ0Q-NrE17oxrrNkU0ZxioUgDeqGycwvNIaMazztM9NyD-dWmZc4dKJsqm0su0CRkMSWcYPPaeIcsYFI7XSaeC65N4RLIKhUfwIxxE-uODEhcl3mTr9rwZGnVMu3WrY7t6wlbDM9FomPejGM2aJoZ05PinAIYvX3oH5QJ9fam0pVLb-mI3bvkKm2wKAgpc4Dh1ut9sqLWkG8-AwMA4r_oEvLwFzf8clzk34zNyPG7BvlnPle99HjQues690L-fknSdFiXyRZeRThvZop0SWJzvUSR49etmk-OxnMbQE4tCBWZr_khEG5jUDzeKt3PIiXdxmUaaEPHzo6Vl9XIY5
```

### Code Manager webhook query parameters

The following query parameters are permitted in the Code Manager webhook.

The `token` query is mandatory, unless you disable `authenticate_webhook` in the Code Manager configuration.

-   `type`: Required. Specifies which type of post body to expect. Accepts:
    -   `github`: GitHub
    -   `gitlab`: GitLab
    -   `stash`: Bitbucket Server \(Stash\)
    -   `bitbucket`: Bitbucket
    -   `tfs-git`: Team Foundation Server \(resource version 1.0 is supported\)
-   `prefix`: Specifies a prefix for converting branch names to environments.
-   `token`: Specifies the entire PE authorization token.

## Setting up the Code Manager webhook on your Git host

Enter the custom URL you created for Code Manager into your Git server’s webhook form as the payload URL. The content type for webhooks is JSON.

Exactly how you set up your webhook varies, depending on where your Git repos are hosted. For example, in your GitHub repo, click on **Settings** \> **Webhooks & services** to enter the payload URL and enter application/json as the content type.

After you've set up your webhook, your Code Manager setup is complete. When you commit new code and push it to your control repo, the webhook triggers Code Manager, and your code is deployed.

## Testing and troubleshooting a Code Manager webhook

To test and troubleshoot your webhook, review your Git host logs or check the Code Manager troubleshooting guide.

Each of the major repository hosting services \(such as GitHub or Bitbucket\) provides a way to review the logs for your webhook runs, so check their documentation for instructions.

For other issues, check the Code Manager troubleshooting for some common problems and troubleshooting tips.

