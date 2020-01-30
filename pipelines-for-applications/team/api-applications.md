---
layout: default
title: "Pipelines API commands: Applications"
---

## ConnectAppRepo

#### Method

PUT

#### Definition

`https://api.distelli.com/:username/apps/:app_name/:repo?apiToken=:apiToken`

#### Parameters

<table>
	<tbody>
		<tr>
			<td><b>username:</b><br>required</td>
			<td><b>String</b><br>Your Pipelines user name.</td>
		</tr>
		<tr>
			<td><b>apiToken:</b><br>required</td>
			<td><b>String</b><br>Your Pipelines API token.</td>
		</tr>
		<tr>
			<td><b>app_name:</b><br>required</td>
			<td><b>String</b><br>The name of the application you wish to create in Pipelines.</td>
		</tr>
		<tr>
			<td><b>repo_name:</b><br>required</td>
			<td><b>String</b><br>The name of the repository.</td>
		</tr>
    <tr>
      <td><b>repo_owner:</b><br>required</td>
      <td><b>String</b><br>The owner of the repository. This is typically part of the URL when accessing this owner's repository.</td>
    </tr>
    <tr>
      <td><b>repo_provider:</b><br>required</td>
      <td><b>String</b><br>The name of the repository provider. Valid values include:<br><ul><li>BITBUCKET</li><li>BITBUCKETSERVER</li><li>GITHUB</li><li>GITHUBENTERPRISE</li><li>GITLAB</li><li>AZUREDEVOPS</li></ul></td>
    </tr>
    <tr>
      <td><b>repo_id:</b><br>*required for GITLAB</td>
      <td><b>String</b><br>This is the GITLAB Project ID. You can retrieve your GITLAB project id with a curl command:<br><code>curl -XGET --header "PRIVATE-TOKEN: XXXXXXXXXXXXXXXXXXXX" "https://gitlab.com/api/v3/projects/owned"</code></td>
    </tr>
    <tr>
      <td><b>branch:</b><br>required</td>
      <td><b>String</b><br>This is the branch name that you wish to add to the application in Pipelines.</td>
    </tr>
    <tr>
      <td><b>auto_build:</b><br>required</td>
      <td><b>Boolean</b><br>Enables or disables auto build on branch commit (push). Valid values include <b>true</b> or <b>false</b>.</td>
    </tr>
    <tr>
      <td><b>build_server_type:</b><br>required</td>
      <td><b>String</b><br>Determines whether this branch should be built on <b>shared</b> Pipelines build hardware or on a <b>private</b> server. Valid values include:<br><ul><li>SHARED</li><li>PRIVATE</li></ul></td>
    </tr>
    <tr>
      <td><b>install_webhook:</b><br>required</td>
      <td><b>Boolean</b><br>Determines whether a webhook should be installed for this branch. Valid options include <b>true</b> or <b>false</b>.</td>
    </tr>
    <tr>
      <td><b>build_vars:</b><br></td>
      <td><b>Object</b><br>The build environment variables.</td>
    </tr>
	</tbody>
</table>

#### Example

~~~
curl -v -X PUT -H "Content-Type: application/json" "https://api.distelli.com/jdoe/apps/SimpleGoApp/repo?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z" -d '{"repo_name":"MyNewApp","repo_owner":"jdoe","repo_provider":"GITHUB","branch":"master","auto_build":true,"build_vars":[{"Name":"One","Value":"A"},{"Name":"Two","Value":"B"}],"build_server_type":"SHARED","install_webhook":true}'
~~~

#### Result Format

~~~
{
   "app_name":"SimpleGoApp",
   "auto_build":true,
   "branch":"master",
   "build_server_type":"SHARED",
   "repo_name":"MyNewApp"
}
~~~

#### Documentation
This API call will allow you to:
<ul>
  <li>Add a new branch to an already connected application.</li>
  <li>Connect a new repository/branch to an application.</li>
</ul>

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).


#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### app_name

Enter the name of an existing application in Pipelines.

#### repo_name

Enter the name of the repository.

#### repo_owner

Enter the name of the owner of the repository. This is typically the owner name that you see on the URL of the repository.

#### repo_provider

Enter the repo provider. Valid values include:
<ul>
  <li>BITBUCKET</li>
  <li>BITBUCKETSERVER</li>
  <li>GITHUB</li>
  <li>GITHUBENTERPRISE</li>
  <li>GITLAB</li>
  <li>AZUREDEVOPS</li>
</ul>

#### repo_id

This value is only necessary when adding a GITLAB repository. You can use the following command to find your project ID:

~~~
curl -XGET --header "PRIVATE-TOKEN: XXXXXXXXXXXXXXXXXXXX" "https://gitlab.com/api/v3/projects/owned"
~~~


#### branch

The name of the branch to connect to the application.


#### auto_build

Set this option to <b>true</b> or <b>false</b> to indicate whether auto-build is turned on. Auto build will build the repo/branch when ever a commit (push) occurs to the branch.


#### install_webhooks

For auto build to work, webhooks must be set. Valid options include:
<ul>
  <li>true</li>
  <li>false</li>
</ul>

#### build_server_type

This option determines if builds will occur on Pipelines shared build servers or on your private build servers. Valid options include:

<ul>
  <li>SHARED</li>
  <li>PRIVATE</li>
</ul>

> **Important:** The Pipelines build servers use [the IP addresses housed in this GitHub repostory](https://github.com/puppetlabs/pipelines-ips). 
>
> If your organization requires a whitelist of IP adresses, such as for a firewall, we strongly recommend watching this repository for changes. 
>
> If whitelisting IP addresses isn't an option, see our documentation on [setting up your own build servers](./build-hardware.html#use-your-own-build-server). 


#### build_vars

~~~
{
   "build_vars":[
      {
         "Name":"One",
         "Value":"A"
      },
      {
         "Name":"Two",
         "Value":"B"
      }
   ]
}
~~~

## CreateApp

#### Method

PUT

#### Definition

`https://api.distelli.com/:username/apps/:app_name?apiToken=:apiToken`

#### Parameters

<table>
	<tbody>
		<tr>
			<td><b>username:</b><br>required</td>
			<td><b>String</b><br>Your Pipelines user name.</td>
		</tr>
		<tr>
			<td><b>apiToken:</b><br>required</td>
			<td><b>String</b><br>Your Pipelines API token.</td>
		</tr>
		<tr>
			<td><b>app_name:</b><br>required</td>
			<td><b>String</b><br>The name of the application you wish to create in Pipelines.</td>
		</tr>
		<tr>
			<td><b>description:</b><br></td>
			<td><b>String</b><br>A description for the created application</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i -X PUT -H "Content-Type: application/json" "https://api.distelli.com/jdoe/apps/NewApp1?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z" -d '{"description": "This is a description"}'
~~~

#### Result Format

~~~
{
    "app": {
        "api_url": "https://api.distelli.com/jdoe/apps/NewApp1",
        "created": "2015-09-09T17:04:28Z",
        "description": "This is a description",
        "html_url": "https://www.distelli.com/jdoe/apps/NewApp1",
        "name": "NewApp1",
        "owner": "jdoe"
    }
}
~~~

#### Documentation
This API call will allow you to create a new application in your Pipelines account.

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).


#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### app_name

Enter a unique name for your new application.

#### description

Enter a string for your new application description.

~~~
{
    "description": "This is a description"
}
~~~

## BuildApp

#### Method

PUT

#### Definition

`https://api.distelli.com/:username/apps/:app_name/:branch_name/build?apiToken=:apiToken`

#### Parameters

<table>
	<tbody>
		<tr>
			<td><b>username:</b><br>required</td>
			<td><b>String</b><br>Your Pipelines user name.</td>
		</tr>
		<tr>
			<td><b>apiToken:</b><br>required</td>
			<td><b>String</b><br>Your Pipelines API token.</td>
		</tr>
		<tr>
			<td><b>app_name:</b><br>required</td>
			<td><b>String</b><br>The name of the application in Pipelines you wish to build.</td>
		</tr>
		<tr>
			<td><b>branch_name:</b><br>required</td>
			<td><b>String</b><br>The branch of the repository you wish to build.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i -X PUT -H "Content-Type: application/json" "https://api.distelli.com/jdoe/apps/rubyubunturbenvapp/master/build?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

#### Result Format

~~~
{
  "build": {
    "api_url": "https://api.distelli.com/jdoe/builds/7191",
    "app_name": "rubyubunturbenvapp",
    "build_num": "7191",
    "build_server": null,
    "cancelled_by": null,
    "cancelling": null,
    "commit": {
      "branch": "master",
      "commit_id": "2670a8bf48bbc326833b24854c6559b82577a832",
      "committer": "doct15",
      "date": null,
      "msg": "Update distelli-manifest.yml",
      "repo_name": "rubyubunturbenvapp",
      "repo_owner": "doct15",
      "url": "https://github.com/doct15/rubyubunturbenvapp/commit/2670a8bf48bbc326833b24854c6559b82577a832"
    },
    "create_time": "2015-11-23T21:06:83Z",
    "dependencies": null,
    "deployments": null,
    "end_time": null,
    "html_url": "https://www.distelli.com/jdoe/builds/7191",
    "release_url": null,
    "release_version": null,
    "start_time": null,
    "status": "Queued"
  }
}
~~~

#### Documentation

This API call will allow you to initiate a new build of an application in your Puppet Pipelines account.

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).


#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### app_name

Enter the name of the application you wish to build.

Application name can be found:

* In the Pipelines web UI [Viewing an application](./application-manage.html).
* With the Pipelines API ListApps command.

#### branch_name

Enter a string for your the application repository branch name you wish to build.

> **Note:** The branch must already be integrated to the application in Pipelines. See [Integrating with a Repository](./integrate-repo.html) for more information.

## GetApp

#### Method

GET

#### Definition

`https://api.distelli.com/:username/apps/:app_name?apiToken=:apiToken`

#### Parameters

<table>
	<tbody>
		<tr>
			<td><b>username:</b><br>required</td>
			<td><b>String</b><br>Your Pipelines user name.</td>
		</tr>
		<tr>
			<td><b>apiToken:</b><br>required</td>
			<td><b>String</b><br>Your Pipelines API token.</td>
		</tr>
		<tr>
			<td><b>app_name:</b><br>required</td>
			<td><b>String</b><br>The name of the application you wish to view.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i "https://api.distelli.com/jdoe/apps/SimpleApp?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

#### Result Format

~~~
{
    "app": {
        "api_url": "https://api.distelli.com/jdoe/apps/SimpleApp",
        "created": "2015-09-02T15:00:33Z",
        "description": null,
        "html_url": "https://www.distelli.com/jdoe/apps/SimpleApp",
        "name": "SimpleApp",
        "owner": "jdoe"
    }
}
~~~

#### Documentation
This API call will allow you to view a specific application.

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).


#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### app_name

Enter the name of the application you wish to see branches.

Application name can be found:

* In the Pipelines web UI [Viewing an application](./application-manage.html).
* With the Pipelines API ListApps command.

## GetAppBranches

#### Method

GET

#### Definition

`https://api.distelli.com/:username/apps/:app_name/branches?apiToken=:apiToken`

#### Parameters

<table>
	<tbody>
		<tr>
			<td><b>username:</b><br>required</td>
			<td><b>String</b><br>Your Pipelines user name.</td>
		</tr>
		<tr>
			<td><b>apiToken:</b><br>required</td>
			<td><b>String</b><br>Your Pipelines API token.</td>
		</tr>
		<tr>
			<td><b>app_name:</b><br>required</td>
			<td><b>String</b><br>The name of the application you wish to view branches.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i "https://api.distelli.com/jdoe/apps/SimpleApp/branches?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

#### Result Format

~~~
{
   "branches":[
      {
         "name":"develop"
      },
      {
         "name":"feature-test8/15"
      },
      {
         "name":"feature815a"
      },
      {
         "name":"master"
      },
      {
         "name":"rc-3.0.4"
      },
      {
         "name":"test20160514a_a-ABC"
      }
   ]
}
~~~

#### Documentation

This API call will allow you to view an application's attached branches.

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).


#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### app_name

Enter the name of the application whose branches you wish to see.

Application name can be found:

* In the Pipelines web UI [Viewing an application](./application-manage.html).
* With the Pipelines API ListApps command.

## DeleteAppBranches

#### Method

DELETE

#### Definition

`https://api.distelli.com/:username/apps/:app_name/branches?apiToken=:apiToken&branch=:branch&branch=:branch`

#### Parameters

<table>
	<tbody>
		<tr>
			<td><b>username:</b><br>required</td>
			<td><b>String</b><br>Your Pipelines user name.</td>
		</tr>
		<tr>
			<td><b>apiToken:</b><br>required</td>
			<td><b>String</b><br>Your Pipelines API token.</td>
		</tr>
		<tr>
			<td><b>app_name:</b><br>required</td>
			<td><b>String</b><br>The name of the application you wish to delete in Pipelines.</td>
		</tr>
		<tr>
			<td><b>branch:</b><br>required</td>
			<td><b>String</b><br>The name of the application branch you wish to remove from the application. You can include multiple branch=:branch entries.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i -X DELETE -H "Content-Type: application/json" "https://api.distelli.com/jdoe/apps/SimpleApp/branches?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z&branch=feature-test8%2F15&branch=develop"
~~~

#### Result Format

~~~
NO RESULTS ON SUCCESS
~~~

#### Documentation

This API call will allow you to delete an applications branch(es) on your application in Pipelines.

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).


#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### app_name

Enter the name of the application you wish to remove branch(es) from.

Application name can be found:

* In the Pipelines web UI [Viewing an application](./application-manage.html).
* With the Pipelines API ListApps command.

#### branch

Enter the name of the branch you wish to remove from the application.

> **Caution:** This must be URL encoded.

Branch names can be found:

* With the Pipelines API GetAppBranches command.

## ListAppBuilds

#### Method

GET

#### Definition

`https://api.distelli.com/:username/apps/:app_name/builds?apiToken=:apiToken`

#### Parameters

<table>
	<tbody>
		<tr>
			<td><b>username:</b><br>required</td>
			<td><b>String</b><br>Your Pipelines user name.</td>
		</tr>
		<tr>
			<td><b>apiToken:</b><br>required</td>
			<td><b>String</b><br>Your Pipelines API token.</td>
		</tr>
		<tr>
			<td><b>app_name:</b><br>required</td>
			<td><b>String</b><br>The name of your application in Pipelinesyou wish to list builds.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i "https://api.distelli.com/jdoe/apps/gobuilddeploy/builds?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

#### Result Format

~~~
{
    "api_url": "https://api.distelli.com/jdoe/apps/gobuilddeploy/builds",
    "builds": [
         {
            "api_url": "https://api.distelli.com/jdoe/builds/1720",
            "app_name": "gobuilddeploy",
            "build_num": "1720",
            "build_server": null,
            "commit": {
                "branch": "master",
                "commit_id": "e8836d20c3030addd25fa68ad5fde32e0fb7c444",
                "committer": "doct15",
                "date": "2015-09-14T19:04:00Z",
                "msg": "Trigger build",
                "repo_name": "gobuilddeploy",
                "repo_owner": "doct15",
                "url": "https://github.com/doct15/gobuilddeploy/commit/e8836d20c3030addd25fa68ad5fde32e0fb7c444"
            },
            "create_time": "2015-09-14T19:04:47Z",
            "dependencies": null,
            "deployments": [
                "https://api.distelli.com/jdoe/deployments/d-j56fivknw358efa96e2"
            ],
            "end_time": "2015-09-14T19:04:87Z",
            "html_url": "https://www.distelli.com/jdoe/builds/1720",
            "release_url": "https://api.distelli.com/jdoe/apps/gobuilddeploy/releases/v25",
            "release_version": "v25",
            "start_time": "2015-09-14T19:04:78Z",
            "status": "Success"
        }
    ],
    "marker": null
}
~~~

#### Documentation

This API call will allow you to list an application's builds.

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).


#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### app_name

Application name can be found:

* In the Pipelines web UI [Viewing an application](./application-manage.html).
* With the Pipelines API ListApps command.

#### Pagination

This API command supports pagination and sorting. The following query string parameters can be included:

`max_results` - The number of results to return. The default is 10.

`marker` - Continue pagination from this **marker**.

`order` - Provide sorting values include: **asc** | **desc** for ascending | descending respectively.


For more information on pagination see [Pipelines API pagination](./api.html#pagination).

## ListAppEnvs

#### Method

GET

#### Definition

`https://api.distelli.com/:username/apps/:app_name/envs?apiToken=:apiToken`

#### Parameters

<table>
	<tbody>
		<tr>
			<td><b>username:</b><br>required</td>
			<td><b>String</b><br>Your Pipelines user name.</td>
		</tr>
		<tr>
			<td><b>apiToken:</b><br>required</td>
			<td><b>String</b><br>Your Pipelines API token.</td>
		</tr>
		<tr>
			<td><b>app_name:</b><br>required</td>
			<td><b>String</b><br>The name of your application in Pipelines you wish to list environments.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i "https://api.distelli.com/jdoe/apps/SimpleApp/envs?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

#### Result Format

~~~
{
    "envs": [
        {
            "active_release_url": null,
            "active_release_version": null,
            "api_url": "https://api.distelli.com/jdoe/envs/SA_Windows",
            "app_name": "SimpleApp",
            "app_url": "https://api.distelli.com/jdoe/apps/SimpleApp",
            "deployments_url": "https://api.distelli.com/jdoe/envs/SA_Windows/deployments",
            "description": null,
            "html_url": "https://www.distelli.com/jdoe/envs/SA_Windows",
            "last_deployment_url": null,
            "name": "SA_Windows",
            "owner": "jdoe",
            "servers_url": "https://api.distelli.com/jdoe/envs/SA_Windows/servers",
            "settings_url": "https://api.distelli.com/jdoe/envs/SA_Windows/settings",
            "tags": [
                "test",
                "win"
            ],
            "vars": []
        },
        {
            "active_release_url": "https://api.distelli.com/jdoe/apps/SimpleApp/releases/76",
            "active_release_version": "v76"
            "api_url": "https://api.distelli.com/jdoe/envs/SA_Linux",
            "app_name": "SimpleApp",
            "app_url": "https://api.distelli.com/jdoe/apps/SimpleApp",
            "deployments_url": "https://api.distelli.com/jdoe/envs/SA_Linux/deployments",
            "description": "This is SA_Linux Description. Go Hawks",
            "html_url": "https://www.distelli.com/jdoe/envs/SA_Linux",
            "last_deployment_url": "https://api.distelli.com/jdoe/deployments/37363",
            "name": "SA_Linux",
            "owner": "jdoe",
            "servers_url": "https://api.distelli.com/jdoe/envs/SA_Linux/servers",
            "settings_url": "https://api.distelli.com/jdoe/envs/SA_Linux/settings",
            "tags": [
                "egg",
                "qa"
            ],
            "vars": [
                {
                    "name": "EXAMPLE",
                    "value": "\"OVERRIDDEN\""
                },
                {
                    "name": "EXAMPLE2",
                    "value": "\"From WebUI!!\""
                }
            ]
        },
        {
            "active_release_url": null,
            "active_release_version": null
            "api_url": "https://api.distelli.com/jdoe/envs/TEST_SAPP",
            "app_name": "SimpleApp",
            "app_url": "https://api.distelli.com/jdoe/apps/SimpleApp",
            "deployments_url": "https://api.distelli.com/jdoe/envs/TEST_SAPP/deployments",
            "description": null,
            "html_url": "https://www.distelli.com/jdoe/envs/TEST_SAPP",
            "last_deployment_url": null,
            "name": "TEST_SAPP",
            "owner": "jdoe",
            "servers_url": "https://api.distelli.com/jdoe/envs/TEST_SAPP/servers",
            "settings_url": "https://api.distelli.com/jdoe/envs/TEST_SAPP/settings",
            "tags": [],
            "vars": [
                {
                    "name": "EXAMPLE",
                    "value": "OVERRIDDEN"
                }
            ]
        }
    ],
    "marker": null
}
~~~

#### Documentation

This API call will allow you to list an applications environments.

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).


#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### app_name

Application name can be found:

* In the Pipelines web UI [Viewing an application](./application-manage.html).
* With the Pipelines API ListApps command.

#### Pagination

This API command supports pagination and sorting. The following query string parameters can be included:

`max_results` - The number of results to return. The default is 10.

`marker` - Continue pagination from this **marker**.

`order` - Provide sorting values include: **asc** | **desc** for ascending | descending respectively.


For more information on pagination see [Pipelines API pagination](./api.html#pagination).

## ListAppInstances

#### Method

GET

#### Definition

`https://api.distelli.com/:username/apps/:app_name/instances?apiToken=:apiToken`

#### Parameters

<table>
	<tbody>
		<tr>
			<td><b>username:</b><br>required</td>
			<td><b>String</b><br>Your Pipelines user name.</td>
		</tr>
		<tr>
			<td><b>apiToken:</b><br>required</td>
			<td><b>String</b><br>Your Pipelines API token.</td>
		</tr>
		<tr>
			<td><b>app_name:</b><br>required</td>
			<td><b>String</b><br>The name of your application in Pipelines you wish to list deployed instances.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i "https://api.distelli.com/jdoe/apps/SimpleApp/instances?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

#### Result Format

~~~
{
   "instances":[
      {
         "api_url":"https://api.distelli.com/jdoe/apps/SimpleApp/instances",
         "deployed":"1970-01-01T00:00:00Z",
         "environment":"SA_Linux",
         "environment_url":"https://api.distelli.com/jdoe/releases/a-y9hz57fmny866e948b65",
         "html_url":"https://www.distelli.com/jdoe/apps/SimpleApp",
         "name":"SimpleApp",
         "release_url":null,
         "release_version":"v76",
         "server_id":"766b88c8-e925-11e4-ae8b-080027cc07f7",
         "server_url":"https://api.distelli.com/jdoe/servers/766b88c8-e925-11e4-ae8b-080027cc07f7",
         "state":"Stopped"
      }
   ],
   "marker":null
}
~~~

#### Documentation

This API call will allow you to list an application's deployed instances.

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).


#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### app_name

Application name can be found:

* In the Pipelines web UI [Viewing an application](./application-manage.html).
* With the Pipelines API ListApps command.

#### Pagination

This API command supports pagination and sorting. The following query string parameters can be included:

`max_results` - The number of results to return. The default is 10.

`marker` - Continue pagination from this **marker**.

`order` - Provide sorting values include: **asc** | **desc** for ascending | descending respectively.

For more information on pagination see [Pipelines API pagination](./api.html#pagination).

## ListApps

#### Method

GET

#### Definition

`https://api.distelli.com/:username/apps?apiToken=:apiToken`

#### Parameters

<table>
	<tbody>
		<tr>
			<td><b>username:</b><br>required</td>
			<td><b>String</b><br>Your Pipelines user name.</td>
		</tr>
		<tr>
			<td><b>apiToken:</b><br>required</td>
			<td><b>String</b><br>Your Pipelines API token.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i "https://api.distelli.com/jdoe/apps?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z&max_results=3&sort=asc"
~~~

#### Result Format

~~~
{
  "marker": "J8VQkuw95vivBXFwuVcoiQ==",
  "apps": [
    {
      "releases_url": "https://api-beta.distelli.com/jdoe/apps/bitbucket_git/releases",
      "owner": "jdoe",
      "name": "bitbucket_git",
      "api_url": "https://api-beta.distelli.com/jdoe/apps/bitbucket_git",
      "builds_url": "https://api-beta.distelli.com/jdoe/apps/bitbucket_git/builds",
      "created": "2016-03-09T19:07:38.456Z",
      "deployments_url": "https://api-beta.distelli.com/jdoe/apps/bitbucket_git/deployments",
      "description": null,
      "html_url": "https://www-beta.distelli.com/jdoe/apps/bitbucket_git",
      "latest_build": {
        "status": "s",
        "build_branch": "TAG_TEST_20160309110707837865829",
        "build_num": "5180",
        "build_time": "2016-03-09T19:07:33.046Z",
        "build_url": "https://api-beta.distelli.com/jdoe/builds/5180",
        "build_user": "bmcgehee",
        "commit_id": "841efca0d813699b0404c4f7b85c944c5c84f1a3",
        "commit_url": "https://bitbucket.org/bmcgehee/bitbucket_git/commits/841efca0d813699b0404c4f7b85c944c5c84f1a3",
        "repo_name": "bitbucket_git"
      },
      "latest_release": {
        "release_version": "v22",
        "release_url": "https://api-beta.distelli.com/jdoe/apps/bitbucket_git/releases/v22",
        "release_time": "2016-03-09T19:07:38.481Z"
      }
    },
    {
      "releases_url": "https://api-beta.distelli.com/jdoe/apps/bitbucket_hg/releases",
      "owner": "jdoe",
      "name": "bitbucket_hg",
      "api_url": "https://api-beta.distelli.com/jdoe/apps/bitbucket_hg",
      "builds_url": "https://api-beta.distelli.com/jdoe/apps/bitbucket_hg/builds",
      "created": "2016-08-04T00:42:32.982Z",
      "deployments_url": "https://api-beta.distelli.com/jdoe/apps/bitbucket_hg/deployments",
      "description": null,
      "html_url": "https://www-beta.distelli.com/jdoe/apps/bitbucket_hg",
      "latest_build": {
        "status": "s",
        "build_branch": "default",
        "build_num": "7240",
        "build_time": "2016-08-04T00:42:27.945Z",
        "build_url": "https://api-beta.distelli.com/jdoe/builds/7240",
        "build_user": "bmcgehee",
        "commit_id": "16a10e6d6d7d6ebf04eec52ecfc93973abcd6948",
        "commit_url": "https://bitbucket.org/bmcgehee/bitbucket_hg/commits/16a10e6d6d7d6ebf04eec52ecfc93973abcd6948",
        "repo_name": "bitbucket_hg"
      },
      "latest_release": {
        "release_version": "v7",
        "release_url": "https://api-beta.distelli.com/jdoe/apps/bitbucket_hg/releases/v7",
        "release_time": "2016-08-04T00:42:33.001Z"
      }
    },
    {
      "releases_url": "https://api-beta.distelli.com/jdoe/apps/DistelliTest/releases",
      "owner": "jdoe",
      "name": "DistelliTest",
      "api_url": "https://api-beta.distelli.com/jdoe/apps/DistelliTest",
      "builds_url": "https://api-beta.distelli.com/jdoe/apps/DistelliTest/builds",
      "created": "2016-05-27T17:48:11.142Z",
      "deployments_url": "https://api-beta.distelli.com/jdoe/apps/DistelliTest/deployments",
      "description": null,
      "html_url": "https://www-beta.distelli.com/jdoe/apps/DistelliTest",
      "latest_build": {
        "status": "s",
        "build_branch": "master",
        "build_num": "6416",
        "build_time": "2016-05-27T17:48:07.109Z",
        "build_url": "https://api-beta.distelli.com/jdoe/builds/6416",
        "build_user": "baveltman",
        "commit_id": "9160772afbd009b9f36638369214998d895a34fe",
        "commit_url": "https://github.com/baveltman/distellitest/commit/9160772afbd009b9f36638369214998d895a34fe",
        "repo_name": "DistelliTest"
      },
      "latest_release": {
        "release_version": "v2",
        "release_url": "https://api-beta.distelli.com/jdoe/apps/DistelliTest/releases/v2",
        "release_time": "2016-05-27T17:48:11.179Z"
      }
    }
  ]
}
~~~

#### Documentation

This API call will allow you to list all applications.

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).


#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### Pagination

This API command supports pagination and sorting. The following query string parameters can be included:

`max_results` - The number of results to return. The default is 10.

`marker` - Continue pagination from this **marker**.

`order` - Provide sorting values include: **asc** | **desc** for ascending | descending respectively.

For more information on pagination see [Pipelines API pagination](./api.html#pagination).


