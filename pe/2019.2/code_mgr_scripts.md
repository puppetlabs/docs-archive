# Triggering Code Manager with custom scripts

Custom scripts are a good way to trigger deployments if you have requirements such as existing continuous integration systems, privately hosted Git repos, or custom notifications.

Alternatively, a webhook provides a simpler way to trigger deployments automatically.

To create a script that triggers Code Manager to deploy your environments, you can use either the `puppet-code` command or a `curl` statement that hits the endpoints. We recommend the `puppet-code` command.

Either way, after you have composed your script, you can trigger deployment of new code into your environments. Commit new code, push it to your control repo, and run your script to trigger Code Manager to deploy your code.

All of these instructions assume that you have enabled and configured Code Manager.

**Related information**  


[Request an authentication token for deployments](code_mgr_config.md#)

[Code Manager API](code_manager_api.md#)

## Deploying environments with the `puppet-code` command

The `puppet-code` command allows you to trigger environment deployments from the command line. You can use this command in your custom scripts.

For example, to deploy all environments and then return deployment results with commit SHAs for each of your environments, incorporate this command into your script:

```
puppet-code deploy --all --wait
```

**Related information**  


[Triggering Code Manager on the command line](puppet_code.md#)

## Deploying environments with a curl command

To trigger Code Manager code deployments with a custom script, compose a `curl` command to hit the Code Manager`/deploys` endpoint.

To deploy environments with a `curl` command in your custom script, compose a `curl` command to hit the `/v1/deploys` endpoint.

Specify either the `"deploy-all"` key, to deploy all configured environments, or the `"environments"` key, to deploy a specified list of environments. By default, Code Manager returns queuing results immediately after accepting the deployment request.

 If you prefer to get complete deployment results after Code Manager finishes processing the deployments, specify the optional `"wait"` key with your request. In deployments that are geographically dispersed or have a large number of environments, completing code deployment can take up to several minutes.

For complete information about Code Manager endpoints, request formats, and responses, see the Code Manager API documentation.

You must include a custom URL for Code Manager and a PE authentication token in any request to a Code Manager endpoint.

### Creating a custom URL for Code Manager scripts

To trigger deployments with a `curl` command in a custom script, you’ll need a custom Code Manager URL. This URL is composed of:

-   Your Puppet master server name \(for example, `master.example.com`\)
-   The Code Manager port \(for example, 8170\)
-   The endpoint you want to hit \(for example, `/code-manager/v1/deploys/`\)
-   The authentication token you generated earlier \(`token=<TOKEN>`\)

A typical URL for use with a `curl` command might look something like this:

```
https://master.example.com:8170/code-manager/v1/deploys&token=<TOKEN>
```

### Attaching an authentication token

You must attach a PE authentication token to any request to Code Manager endpoints. You can either:

-   Specify the path to the token with `cat ~/.puppetlabs/token` in the body of your request. For example:

    ```
    $ curl --cacert $(puppet agent --configprint cacert) -X POST -H 'Content-Type: application/json' -H "X-Authentication: `cat ~/.puppetlabs/token`" https://localhost:8170/code-manager/v1/deploys -d '{"environments": ["production", "testing"]}'
    ```

-   Attach the entire token to your Code Manager URL using the `token` query parameter. For example:

    ```
    https://master.example.com:8170/code-manager/v1/webhook?type=github&token=<TOKEN>
    ```


If you are using a `curl` command to hit the `/webhook` endpoint directly, you must attach the entire token.

## Checking deployment status with a curl commmand

You can check the status of a deployment by hitting the `status` endpoint.

To use a `curl` command in your custom script, compose a `curl` command to hit the status endpoint. You must incorporate a custom URL for Code Manager in the script. 

