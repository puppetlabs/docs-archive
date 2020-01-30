---
layout: default
title: "Pipelines API commands: Deploys"
---

## DeployEnv

#### Method

POST

#### Definition

`https://api.distelli.com/:username/envs/:env_name/deploy?apiToken=:apiToken`

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
			<td><b>env_name:</b><br>required</td>
			<td><b>String</b><br>The name of the application environment you wish to deploy to.</td>
		</tr>
    <tr>
        <td><b>release_version:</b><br>required</td>
        <td><b>String</b><br>The application release version you wish to deploy.</td>
    </tr>
		<tr>
			<td><b>description:</b><br></td>
			<td><b>String</b><br>The description for the deploy.</td>
		</tr>
		<tr>
			<td><b>sync_only:</b><br></td>
			<td><b>Boolean</b><br>If <code>true</code>, deploys the release only to servers in the environment that do not currently have that release.</td>
		</tr>
		<tr>
			<td><b>stagger_size:</b><br></td>
			<td><b>Integer</b><br>The number of servers to deploy to at a time.</td>
		</tr>
		<tr>
			<td><b>stagger_delay:</b><br></td>
			<td><b>Integer</b><br>The delay to wait between deploys.</td>
		</tr>

	</tbody>
</table>

#### Examples

~~~
curl -i -H "Content-Type: application/json" -X POST "https://api.distelli.com/jdoe/envs/SA_Linux/deploy?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z" -d '{"release_version": "v76","description": "Deploying release 76","stagger_size": 4,"stagger_delay": 14}'
~~~

#### Result Format

~~~
{
    "deployment": {
        "api_url": "https://api.distelli.com/jdoe/deployments/37439",
        "app_name": "SimpleApp",
        "app_url": "https://api.distelli.com/jdoe/apps/SimpleApp",
        "deployer": "jdoe",
        "deployment_id": "d-g6ypsiqe0t5b5208f23d",
        "deployment_number": "37439",
        "deployment_state": "InProgress",
        "deployment_time": "2015-09-15T00:04:46Z",
        "deployment_type": "Deploy",
        "env_name": "SA_Linux",
        "env_url": "https://api.distelli.com/jdoe/envs/SA_Linux",
        "html_url": "https://www.distelli.com/jdoe/deployments/37439",
        "release_url": "https://api.distelli.com/jdoe/apps/SimpleApp/releases/v76",
        "release_version": "v76",
        "servers": {
            "done": 0,
            "failed": 0,
            "in_progress": 0,
            "pending": 0,
            "waiting": 1
        },
        "servers_url": "https://api.distelli.com/jdoe/deployments/d-g6ypsiqe0t5b5208f23d/servers",
        "stagger_delay": 14,
        "stagger_size": 4
    }
}
~~~

#### Documentation
This API call will allow you to add or remove servers from an application environment. While adding servers you can specify to also deploy a release to the newly added servers.

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).


#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### env_name

Environment name can be found:

* In the Pipelines web UI [Viewing an environment's deployments](./environment.html).
* With the Pipelines API ListEnvs command.
* With the Pipelines API ListAppEnvs command.

#### release_version

The application release version that you wish to deploy to the environment.

~~~
{
    "release_version": "v76"
}
~~~

#### description

Enter a description string for the deploy.

~~~
{
    "description": "This is a description"
}
~~~

#### sync_only

If true, deploy the release to servers in the environment that do not currently have that release.

~~~
{
    "sync_only": true
}
~~~


#### stagger_size

The number of servers to deploy to at a time.

~~~
{
    "stagger_size": 2
}
~~~

#### stagger_delay

The delay to wait, in seconds, between deploys.

~~~
{
    "stagger_size": 15
}
~~~

## GetDeployment

#### Method

GET

#### Definition

`https://api.distelli.com/:username/deployments/:deployment_id?apiToken=:apiToken`

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
			<td><b>deployment_id:</b><br>required</td>
			<td><b>String</b><br>The Pipelinesdeployment ID.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i "https://api.distelli.com/jdoe/deployments/37007?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

#### Result Format

~~~
{
    "deployment": {
        "api_url": "https://api.distelli.com/jdoe/deployments/37007",
        "app_name": "SimpleApp",
        "app_url": "https://api.distelli.com/jdoe/apps/SimpleApp",
        "deployer": "jdoe",
        "deployment_id": "d-b3fjskvt64e2dbe47a76",
        "deployment_number": "37007",
        "deployment_state": "Ok",
        "deployment_time": "2015-09-10T18:30:94Z",
        "deployment_type": "Deploy",
        "env_name": "SA_Linux",
        "env_url": "https://api.distelli.com/jdoe/envs/SA_Linux",
        "html_url": "https://www.distelli.com/jdoe/deployments/37007",
        "release_id": "a-y9hz57fmny866e948b65",
        "release_url": "https://api.distelli.com/jdoe/releases/a-y9hz57fmny866e948b65",
        "release_version": "v76",
        "servers": {
            "done": 1,
            "failed": 0,
            "in_progress": 0,
            "pending": 0,
            "waiting": 0
        },
        "servers_url": "https://api.distelli.com/jdoe/deployments/d-b3fjskvt64e2dbe47a76/servers",
        "stagger_delay": 60,
        "stagger_size": 1
    }
}
~~~

The `deployment_state` may be any of these values: `Ok`, `Failed`, `InProgress`, `Aborted`, `Pending`.

#### Documentation
This API call will allow you to view a specific deployment.

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).


#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### deployment_id

Deployment IDs can be found:

* In the Pipelines web UI [Viewing a deployment](./application-deploy.html).
* With the API ListDeployments command.

# Results

#### deployment_state

One of the following:
<ul>
    <li>Ok(0)</li>
    <li>Failed(1)</li>
    <li>InProgress(2)</li>
    <li>Aborted(3)</li>
    <li>Pending(4)</li>
</ul>

## ListDeployments

#### Method

GET

#### Definition

`https://api.distelli.com/:username/apps/:app_name/deployments?apiToken=:apiToken`

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
         <td><b>String</b><br>The name of the application you wish to list deployments.</td>
      </tr>
	</tbody>
</table>

#### Examples

~~~
curl -i "https://api.distelli.com/jdoe/apps/SimpleApp/deployments?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z&max_results=2"
~~~

#### Result Format

~~~
{
   "deployments":[
      {
         "api_url":"https://api.distelli.com/jdoe/deployments/28972",
         "app_name":"SimpleApp",
         "app_url":"https://api.distelli.com/jdoe/apps/SimpleApp",
         "deployer":"jdoe",
         "deployment_id":"d-e3mkb84os2a231157007",
         "deployment_number":"28972",
         "deployment_state":"Failed",
         "deployment_time":"2015-06-24T21:02:05Z",
         "deployment_type":"Deploy",
         "env_name":"SA_Linux",
         "env_url":"https://api.distelli.com/jdoe/envs/SA_Linux",
         "html_url":"https://www.distelli.com/jdoe/deployments/28972",
         "release_url":"https://api.distelli.com/jdoe/apps/SimpleApp/releases/v1",
         "release_version":"v1",
         "servers":{
            "done":0,
            "failed":1,
            "in_progress":0,
            "pending":0,
            "waiting":0
         },
         "servers_url":"https://api.distelli.com/jdoe/deployments/d-e3mkb84os2a231157007/servers",
         "stagger_delay":60,
         "stagger_size":1
      },
      {
         "api_url":"https://api.distelli.com/jdoe/deployments/28973",
         "app_name":"SimpleApp",
         "app_url":"https://api.distelli.com/jdoe/apps/SimpleApp",
         "deployer":"jdoe",
         "deployment_id":"d-o9v4rxny9wccee2b79c0",
         "deployment_number":"28973",
         "deployment_state":"Failed",
         "deployment_time":"2015-06-24T21:04:19Z",
         "deployment_type":"Deploy",
         "env_name":"SA_Linux",
         "env_url":"https://api.distelli.com/jdoe/envs/SA_Linux",
         "html_url":"https://www.distelli.com/jdoe/deployments/28973",
         "release_url":"https://api.distelli.com/jdoe/apps/SimpleApp/releases/v1",
         "release_version":"v1",
         "servers":{
            "done":0,
            "failed":1,
            "in_progress":0,
            "pending":0,
            "waiting":0
         },
         "servers_url":"https://api.distelli.com/bmcgehee/deployments/d-o9v4rxny9wccee2b79c0/servers",
         "stagger_delay":60,
         "stagger_size":1
      }
   ],
   "marker":"VNOvsFpQaWxaam7mmtHZkeW2q/d0YbNbAnD8Rlvbj9o="
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

## ListDeploymentServers

#### Method

GET

#### Definition

`https://api.distelli.com/:username/deployments/:deployment_id/servers?apiToken=:apiToken`

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
         <td><b>deployment_id:</b><br>required</td>
         <td><b>String</b><br>The deployment ID you wish to list the servers deployed to.</td>
      </tr>
	</tbody>
</table>

#### Examples

~~~
curl -i "https://api.distelli.com/jdoe/deployments/37363/servers?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

#### Result Format

~~~
{
    "marker": null,
    "servers": [
        {
            "agent_version": "3.35",
            "api_url": "https://api.distelli.com/jdoe/servers/766b88c8-e925-11e4-ae8b-080027cc07f7",
            "cloud_instance_id": null,
            "cloud_location": null,
            "cloud_provider": null,
            "dns_name": "serverA",
            "html_url": "https://www.distelli.com/jdoe/servers/766b88c8-e925-11e4-ae8b-080027cc07f7",
            "ip_addr": "192.168.1.112",
            "is_healthy": "true",
            "mac_address": "08:00:27:cc:07:f7",
            "os_name": "Ubuntu",
            "os_version": "14.04",
            "server_id": "766b88c8-e925-11e4-ae8b-080027cc07f7",
            "start_time": "2015-09-14T19:10:06Z",
            "tags": null
        }
    ]
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


#### deployment_id

The deployment ID can be found:

* With the Pipelines API ListDeployments command.
* With the Pipelines API ListEnvDeployments command.

#### Pagination

This API command supports pagination and sorting. The following query string parameters can be included:

`max_results` - The number of results to return. The default is 10.

`marker` - Continue pagination from this **marker**.

`order` - Provide sorting values include: **asc** | **desc** for ascending | descending respectively.

For more information on pagination see [Pipelines API pagination](./api.html#pagination).

## ListEnvDeployments

#### Method

GET

#### Definition

`https://api.distelli.com/:username/envs/:env_name/deployments?apiToken=:apiToken`

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
			<td><b>env_name:</b><br>required</td>
			<td><b>String</b><br>The application environment you want to list deployments.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i "https://api.distelli.com/jdoe/envs/SA_Linux/deployments?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z8&max_results=2&order=desc"
~~~

#### Result Format

~~~
{
   "deployments":[
      {
         "api_url":"https://api.distelli.com/jdoe/deployments/37363",
         "app_name":"SimpleApp",
         "app_url":"https://api.distelli.com/jdoe/apps/SimpleApp",
         "deployer":"jdoe",
         "deployment_id":"d-06c2zta9oj81303607e4",
         "deployment_number":"37363",
         "deployment_state":"Ok",
         "deployment_time":"2015-09-14T20:25:71Z",
         "deployment_type":"Deploy",
         "env_name":"SA_Linux",
         "env_url":"https://api.distelli.com/jdoe/envs/SA_Linux",
         "html_url":"https://www.distelli.com/jdoe/deployments/37363",
         "release_url":"https://api.distelli.com/jdoe/apps/SimpleApp/releases/v76",
         "release_version":"v76",
         "servers":{
            "done":1,
            "failed":0,
            "in_progress":0,
            "pending":0,
            "waiting":0
         },
         "servers_url":"https://api.distelli.com/jdoe/deployments/d-06c2zta9oj81303607e4/servers",
         "stagger_delay":60,
         "stagger_size":1
      },
      {
         "api_url":"https://api.distelli.com/jdoe/deployments/37342",
         "app_name":"SimpleApp",
         "app_url":"https://api.distelli.com/jdoe/apps/SimpleApp",
         "deployer":"jdoe",
         "deployment_id":"d-y4chitp3ja13f2d843a",
         "deployment_number":"37342",
         "deployment_state":"Ok",
         "deployment_time":"2015-09-14T19:26:54Z",
         "deployment_type":"Deploy",
         "env_name":"SA_Linux",
         "env_url":"https://api.distelli.com/jdoe/envs/SA_Linux",
         "html_url":"https://www.distelli.com/jdoe/deployments/37342",
         "release_url":"https://api.distelli.com/jdoe/apps/SimpleApp/releases/v76",
         "release_version":"v76",
         "servers":{
            "done":1,
            "failed":0,
            "in_progress":0,
            "pending":0,
            "waiting":0
         },
         "servers_url":"https://api.distelli.com/jdoe/deployments/d-y4chitp3ja13f2d843a/servers",
         "stagger_delay":17,
         "stagger_size":3
      }
   ],
   "marker":"1WeBXkO09XNgKVTjCp2Yu3UkkU4ZPDjS7mWqEhIHIuU="
}
~~~

#### Documentation

This API call will allow you to list an application's environments.

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).


#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### env_name

Environment name can be found:

* In the Pipelines web UI [Viewing an environment's deployments](./environment.html).
* With the Pipelines API ListEnvs command.
* With the Pipelines API ListAppEnvs command.

#### Pagination

This API command supports pagination and sorting. The following query string parameters can be included:

`max_results` - The number of results to return. The default is 10.

`marker` - Continue pagination from this **marker**.

`order` - Provide sorting values include: **asc** | **desc** for ascending | descending respectively.

For more information on pagination see [Pipelines API pagination](./api.html#pagination).

