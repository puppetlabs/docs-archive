---
layout: default
title: "Pipelines API commands: Servers"
---

## DeleteServer

#### Method

DELETE

#### Definition

`https://api.distelli.com/:username/servers/:server_id?apiToken=:apiToken`

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
			<td><b>server_id:</b><br>required</td>
			<td><b>String</b><br>The server, you wish to delete, unique ID.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i -X DELETE -H "Content-Type: application/json" "https://api.distelli.com/jdoe/servers/ffc8959a-13ff-0543-b64f-080027f80652?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

#### Result Format

~~~
{
   "server":{
      "agent_version":"3.38",
      "api_url":"https://api.distelli.com/jdoe/servers/9567f775-ba59-8546-ba68-12721545568d",
      "cloud_instance_id":"i-cb24b768",
      "cloud_location":"us-east-1",
      "cloud_provider":"Aws",
      "dns_name":"ip-172-30-1-170",
      "html_url":"https://www.distelli.com/jdoe/servers/9567f775-ba59-8546-ba68-12721545568d",
      "ip_addr":"172.30.1.170",
      "is_healthy":"false",
      "mac_address":"12:72:15:45:56:8d",
      "os_name":"Ubuntu",
      "os_version":"14.04",
      "server_id":"9567f775-ba59-8546-ba68-12721545568d",
      "start_time":"2015-09-14T17:55:19Z",
      "tags":null
   }
}
~~~

#### Documentation

This API call will completely remove the server from Pipelines. Re-installation of the Pipelines agent, on the server, will allow the server to reconnect to Pipelines with a different unique server ID.

> **Important:** This works only with servers running Pipelinesagent version 3.30 and above.

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).

#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### env_name

#### server_id

Your Server ID can be found:

* With the Pipelines API ListServers command.
* With the Pipelines API ListEnvServers command.

## EditEnvServers

#### Method

PATCH

#### Definition

`https://api.distelli.com/:username/envs/:env_name/servers?apiToken=:apiToken`

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
			<td><b>String</b><br>The Pipelines application environment you wish to edit server membership.</td>
		</tr>
		<tr>
			<td><b>servers:</b><br>required</td>
			<td><b>Array of Strings</b><br>A comma-separated list of server IDs that you wish to add or remove.</td>
		</tr>
		<tr>
			<td><b>action:</b><br>required</td>
			<td><b>String</b><br>Whether you want to <b>add</b> or <b>remove</b> the servers from the environment.</td>
		</tr>
		<tr>
			<td><b>deploy:</b><br></td>
			<td><b>Boolean</b><br><b>true</b> | <b>false</b>, do you want to deploy to the servers.</td>
		</tr>
		<tr>
			<td><b>description:</b><br></td>
			<td><b>String</b><br>The description for the deploy, if true.</td>
		</tr>
		<tr>
			<td><b>stagger_size:</b><br></td>
			<td><b>Integer</b><br>The number of servers to deploy to at a time.</td>
		</tr>
		<tr>
			<td><b>stagger_delay:</b><br></td>
			<td><b>Integer</b><br>The delay to wait between deploys.</td>
		</tr>
		<tr>
			<td><b>abort_threshold:</b><br></td>
			<td><b>Integer</b><br>Fail and abort the deploy if <i>abort_threshold</i> servers fail.</td>
		</tr>
		<tr>
			<td><b>release_version:</b><br></td>
			<td><b>String</b><br>The application release version you wish to deploy.</td>
		</tr>

	</tbody>
</table>

#### Examples

~~~
curl -i -X PATCH -H "Content-Type: application/json" "https://api.distelli.com/jdoe/envs/SA_Linux/servers?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z" -d {"servers": ["766b88c8-e925-11e4-ae8b-080027cc07f7","a4d253a3-1668-e64b-86be-122d4227c561"],"action": "add","deploy": true,"description": "zDescriptionz","stagger_size": 3,"stagger_delay": 15,"release_version": "v76"}
~~~

#### Result Format

~~~
{
    "deployment": {
        "api_url": "https://api.distelli.com/jdoe/deployments/37448",
        "app_name": "SimpleApp",
        "app_url": "https://api.distelli.com/jdoe/apps/SimpleApp",
        "deployer": "jdoe",
        "deployment_id": "d-mam4l51gg8e6d4beb8c",
        "deployment_number": "37448",
        "deployment_state": "InProgress",
        "deployment_time": "2015-09-15T00:21:49Z",
        "deployment_type": "Deploy",
        "env_name": "SA_Linux",
        "env_url": "https://api.distelli.com/jdoe/envs/SA_Linux",
        "html_url": "https://www.distelli.com/jdoe/deployments/37448",
        "release_url": "https://api.distelli.com/jdoe/apps/SimpleApp/releases/v76",
        "release_version": "v76",
        "servers": {
            "done": 0,
            "failed": 0,
            "in_progress": 0,
            "pending": 0,
            "waiting": 2
        },
        "servers_url": "https://api.distelli.com/jdoe/deployments/d-mam4l51gg8e6d4beb8c/servers",
        "stagger_delay": 15,
        "stagger_size": 3
    },
    "release": {
        "api_url": "https://api.distelli.com/jdoe/apps/SimpleApp/releases/v76",
        "app_name": "SimpleApp",
        "build_num": null,
        "build_url": null,
        "created": "2015-09-02T15:00:37Z",
        "created_by": "jdoe",
        "description": null,
        "html_url": "https://www.distelli.com/jdoe/releases/a-y9hz57fmny866e948b65",
        "release_version": "v76",
        "tags": [
            "abc"
        ]
    },
    "servers": [
        {
            "agent_version": "3.38",
            "api_url": "https://api.distelli.com/jdoe/servers/a4d253a3-1668-e64b-86be-122d4227c561",
            "cloud_instance_id": "i-bcbe3a1f",
            "cloud_location": "us-east-1",
            "cloud_provider": "Aws",
            "dns_name": "ip-172-30-1-250",
            "html_url": "https://www.distelli.com/jdoe/servers/a4d253a3-1668-e64b-86be-122d4227c561",
            "ip_addr": "172.30.1.250",
            "is_healthy": "false",
            "mac_address": "12:2d:42:27:c5:61",
            "os_name": "Linux",
            "os_version": "3.14.48-33.39.amzn1.x86_64",
            "server_id": "a4d253a3-1668-e64b-86be-122d4227c561",
            "start_time": "2015-09-11T17:05:59Z",
            "tags": [
                "EC2"
            ]
        },
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

This API call will allow you to add or remove servers from an application environment in Pipelines. While adding servers you can specify to also deploy a release to the newly added servers.

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

#### servers

Servers is an array of server_id(s).

~~~
{
    "5eab3056-8529-964a-811e-0800277a90a4",
    "a4d253a3-1668-e64b-86be-122d4227c561"
}
~~~

Server ID can be found:

* With the Pipelines API ListServers command.
* With the Pipelines API ListEnvServers command.


#### action

Action specifies whether you wish to **add** or **remove** the servers from the environment.

~~~
{
    "action": "add"
}
~~~

#### deploy

true|false, do you want to **deploy** to the newly added servers or **terminate** to the newly removed servers.

~~~
{
    "deploy": true
}
~~~

#### description

Enter a description string for the deploy.

~~~
{
    "description": "This is a description"
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
    "stagger_delay": 15
}
~~~

#### abort_threshold

In a large deploy, fail and abort the rest of the deploy if <i>abort_threshold</i> number of servers fail.

~~~
{
    "abort_threshold": 5
}
~~~


#### release_version

The application release version that you wish to deploy on adding servers.

~~~
{
    "release_version": "v76"
}
~~~

## EditServerCapability

#### Method

PATCH

#### Definition

`https://api.distelli.com/:username/servers/:server_id/capability?apiToken=:apiToken`

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
			<td><b>server_id:</b><br>required</td>
			<td><b>String</b><br>A server id that you wish to add or remove capabilities.</td>
		</tr>
		<tr>
			<td><b>action:</b><br></td>
			<td><b>String</b><br>Whether you want to <b>add</b> or <b>remove</b> build capabilities. Use with next field.</td>
		</tr>
		<tr>
			<td><b>capabilities:</b><br></td>
			<td><b>Array of Strings</b><br>A comma-separated list of capabilities you wish to add or remove. Used with previous field.</td>
		</tr>
		<tr>
			<td><b>build_server:</b><br></td>
			<td><b>Boolean</b><br>Whether you wish to mark this server as a build server true|false.</td>
		</tr>

	</tbody>
</table>

#### Examples

~~~
curl -i -X PATCH -H "Content-Type: application/json" "https://api.distelli.com/jdoe/servers/78ec8ab3-866a-0344-8aea-080027c8277c/capability?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z" -d '{"action":"add", "capabilities":["java8", "node"], "build_server":true}'
~~~

#### Result Format

~~~
{
   "build_server":true,
   "capabilities":[
      "node",
      "java8",
      "git"
   ]
}
~~~
NOTE: The capability <b>git</b> already existed before this API call.

#### Documentation

This API call will allow you to add or remove server capabilities.

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).


#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### server_id

Server ID can be found:

* With the Pipelines API ListServers command.
* With the Pipelines API ListEnvServers command.

#### action

Action specifies whether you wish to **add** or **remove** the server capabilities.

~~~
{
    "action": "add"
}
~~~


#### capabilities

Capabilities is an array of strings.

~~~
{
  "capabilities": [
    "node",
    "java8"
  ]
}
~~~

#### build_server

Enter a boolean `true | false` to mark this server as a build server or not.

## EditServerTags

#### Method

PATCH

#### Definition

`https://api.distelli.com/:username/servers/:server_id/tags?apiToken=:apiToken`

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
			<td><b>server_id:</b><br>required</td>
			<td><b>String</b><br>A server id that you wish to add or remove tags.</td>
		</tr>
		<tr>
			<td><b>tags:</b><br>required</td>
			<td><b>Array of Strings</b><br>A comma-separated list of tags that you wish to add or remove.</td>
		</tr>
		<tr>
			<td><b>action:</b><br>required</td>
			<td><b>String</b><br>Whether you want to <b>add</b> or <b>remove</b> the tag(s) from the server.</td>
		</tr>

	</tbody>
</table>

#### Examples

~~~
curl -i -X PATCH -H "Content-Type: application/json" "https://api.distelli.com/jdoe/servers/78ec8ab3-866a-0344-8aea-080027c8277c/tags?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z" -d '{"tags": ["SILVRE","PROD"],"action": "add"}'
~~~

#### Result Format

~~~
{
  "tags": [
    "SILVER",
    "PROD"
  ]
}
~~~

#### Documentation

This API call will allow you to add or remove server tags.

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).


#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### server_id

Server ID can be found:

* With the Pipelines API ListServers command.
* With the Pipelines API ListEnvServers command.


#### tags

Tags is an array of tag(s).

~~~
{
  "tags": [
    "SILVER",
    "PROD"
  ]
}
~~~

#### action

Action specifies whether you wish to **add** or **remove** the server tag(s).

~~~
{
    "action": "add"
}
~~~

## GetServer

#### Method

GET

#### Definition

`https://api.distelli.com/:username/servers/:server_id?apiToken=:apiToken`

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
			<td><b>server_id:</b><br>required</td>
			<td><b>String</b><br>The Pipelines server ID.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i "https://api.distelli.com/jdoe/servers/a4d253a3-1668-e64b-86be-122d4227c561?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

#### Result Format

~~~
{
    "api_url": "https://api.distelli.com/jdoe/servers/a4d253a3-1668-e64b-86be-122d4227c561",
    "html_url": "https://www.distelli.com/jdoe/servers/a4d253a3-1668-e64b-86be-122d4227c561",
    "server": {
        "agent_version": "3.38",
        "api_url": "https://api.distelli.com/jdoe/servers/a4d253a3-1668-e64b-86be-122d4227c561",
        "cloud_instance_id": "i-bcbe3a1f",
        "cloud_location": "us-east-1",
        "cloud_provider": "Aws",
        "dns_name": "ip-172-30-1-250",
        "html_url": "https://www.distelli.com/jdoe/servers/a4d253a3-1668-e64b-86be-122d4227c561",
        "ip_addr": "172.30.1.250",
        "is_healthy": "true",
        "mac_address": "12:2d:42:27:c5:61",
        "os_name": "Linux",
        "os_version": "3.14.48-33.39.amzn1.x86_64",
        "server_id": "a4d253a3-1668-e64b-86be-122d4227c561",
        "start_time": "2015-09-11T17:05:59Z",
        "tags": [
            "EC2"
        ]
    }
}
~~~

#### Documentation

This API call will allow you to view a specific server.

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).


#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### server_id

Server ID can be found:

* With the Pipelines API ListServers command.
* With the Pipelines API ListEnvServers command.

## GetServerCapability

#### Method

GET

#### Definition

`https://api.distelli.com/:username/servers/:server_id/capability?apiToken=:apiToken`

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
			<td><b>server_id:</b><br>required</td>
			<td><b>String</b><br>The Pipelines server ID.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i "https://api.distelli.com/jdoe/servers/a4d253a3-1668-e64b-86be-122d4227c561/capability?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

#### Result Format

~~~
{
  "build_server": true,
  "build_capabilities": [
    "git",
    "node",
    "java8"
  ]
}
~~~

#### Documentation

This API call will allow you to view a specific server's build-related capabilities.

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).


#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### server_id

Server ID can be found:

* With the Pipelines API ListServers command.
* With the Pipelines API ListEnvServers command.

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

## ListEnvServers

#### Method

GET

#### Definition

`https://api.distelli.com/:username/envs/:env_name/servers?apiToken=:apiToken`

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
         <td><b>String</b><br>The application environment you wish to list servers.</td>
      </tr>
	</tbody>
</table>

#### Examples

~~~
curl -i "https://api.distelli.com/jdoe/envs/SA_Linux/servers?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
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
        },
        {
            "agent_version": "3.38",
            "api_url": "https://api.distelli.com/jdoe/servers/a4d253a3-1668-e64b-86be-122d4227c561",
            "cloud_instance_id": "i-bcbe3a1f",
            "cloud_location": "us-east-1",
            "cloud_provider": "Aws",
            "dns_name": "ip-172-30-1-250",
            "html_url": "https://www.distelli.com/jdoe/servers/a4d253a3-1668-e64b-86be-122d4227c561",
            "ip_addr": "172.30.1.250",
            "is_healthy": "false",
            "mac_address": "12:2d:42:27:c5:61",
            "os_name": "Linux",
            "os_version": "3.14.48-33.39.amzn1.x86_64",
            "server_id": "a4d253a3-1668-e64b-86be-122d4227c561",
            "start_time": "2015-09-11T17:05:59Z",
            "tags": [
                "EC2"
            ]
        }
    ]
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

## ListServerInstances

#### Method

GET

#### Definition

`https://api.distelli.com/:username/servers/:server_id/apps?apiToken=:apiToken`

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
            <td><b>server_id:</b><br>required</td>
            <td><b>String</b><br>The server ID whose instances you wish to list.</td>
        </tr>
	</tbody>
</table>

#### Examples

~~~
curl -i "https://api.distelli.com/jdoe/servers/766b88c8-e925-11e4-ae8b-080027cc07f7/apps?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

#### Result Format

~~~
{
    "apps": [
        {
            "app_name": "BashSimpleApp",
            "app_url": "https://api.distelli.com/jdoe/apps/BashSimpleApp",
            "deploy_time": "1970-01-01T00:00:00Z",
            "env_name": "BASH_CENTOS",
            "env_url": "https://api.distelli.com/jdoe/envs/BASH_CENTOS",
            "release_url": "https://api.distelli.com/jdoe/apps/BashSimpleApp/releases/v4",
            "release_version": "4",
            "server_id": "766b88c8-e925-11e4-ae8b-080027cc07f7",
            "server_url": "https://api.distelli.com/jdoe/servers/766b88c8-e925-11e4-ae8b-080027cc07f7"
        },
        {
            "app_name": "gobuilddeploy",
            "app_url": "https://api.distelli.com/jdoe/apps/gobuilddeploy",
            "deploy_time": "2015-09-15T00:11:00Z",
            "env_name": "gobuilddeploy-beta",
            "env_url": "https://api.distelli.com/jdoe/envs/gobuilddeploy-beta",
            "release_url": "https://api.distelli.com/jdoe/apps/gobuilddeploy/releases/v26",
            "release_version": "26",
            "server_id": "766b88c8-e925-11e4-ae8b-080027cc07f7",
            "server_url": "https://api.distelli.com/jdoe/servers/766b88c8-e925-11e4-ae8b-080027cc07f7"
        },
        {
            "app_name": "SimpleApp",
            "app_url": "https://api.distelli.com/jdoe/apps/SimpleApp",
            "deploy_time": "1970-01-01T00:00:00Z",
            "env_name": "SA_Linux",
            "env_url": "https://api.distelli.com/jdoe/envs/SA_Linux",
            "release_url": "https://api.distelli.com/jdoe/apps/SimpleApp/releases/v76",
            "release_version": "76",
            "server_id": "766b88c8-e925-11e4-ae8b-080027cc07f7",
            "server_url": "https://api.distelli.com/jdoe/servers/766b88c8-e925-11e4-ae8b-080027cc07f7"
        }
    ],
    "marker": null
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

#### server_id

Server ID can be found:

* With the Pipelines API ListServers command.
* With the Pipelines API ListEnvServers command.

#### Pagination

This API command supports pagination and sorting. The following query string parameters can be included:

`max_results` - The number of results to return. The default is 10.

`marker` - Continue pagination from this **marker**.

`order` - Provide sorting values include: **asc** | **desc** for ascending | descending respectively.

For more information on pagination see [Pipelines API pagination](./api.html#pagination).

## ListServers

#### Method

GET

#### Definition

`https://api.distelli.com/:username/servers/tags/:tag_name?apiToken=:apiToken`

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
			<td><b>tag_name:</b><br>required</td>
			<td><b>String</b><br>The server tag being listed.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i "https://api.distelli.com/jdoe/servers/tags/GOLD?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

#### Result Format

~~~
{
   "marker":null,
   "servers":[
      {
         "api_url":"https://api.distelli.com/jdoe/servers/170d4bd1-7e4a-5044-a138-000d3a718e12",
         "html_url":"https://www.distelli.com/jdoe/servers/170d4bd1-7e4a-5044-a138-000d3a718e12",
         "server_id":"170d4bd1-7e4a-5044-a138-000d3a718e12"
      },
      {
         "api_url":"https://api.distelli.com/jdoe/servers/3f3843f0-1bd7-7f46-b3db-0800273787c2",
         "html_url":"https://www.distelli.com/jdoe/servers/3f3843f0-1bd7-7f46-b3db-0800273787c2",
         "server_id":"3f3843f0-1bd7-7f46-b3db-0800273787c2"
      }
   ]
}
~~~

#### Documentation

This API call will allow you to list servers based on a tag.

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).


#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### tag_name

The tag name that you are searching for.

> **Note:** If the tag name contains a space it must be URL encoded. Using a **+** works.

#### Pagination

This API command supports pagination and sorting. The following query string parameters can be included:

`max_results` - The number of results to return. The default is 10.

`marker` - Continue pagination from this **marker**.

`order` - Provide sorting values include: **asc** | **desc** for ascending | descending respectively.

For more information on pagination see [Pipelines API pagination](./api.html#pagination).

## ListServersByTag

#### Method

GET

#### Definition

`https://api.distelli.com/:username/servers/tags/:tag_name?apiToken=:apiToken`

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
			<td><b>tag_name:</b><br>required</td>
			<td><b>String</b><br>The server tag being listed.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i "https://api.distelli.com/jdoe/servers/tags/GOLD?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

#### Result Format

~~~
{
   "marker":null,
   "servers":[
      {
         "api_url":"https://api.distelli.com/jdoe/servers/170d4bd1-7e4a-5044-a138-000d3a718e12",
         "html_url":"https://www.distelli.com/jdoe/servers/170d4bd1-7e4a-5044-a138-000d3a718e12",
         "server_id":"170d4bd1-7e4a-5044-a138-000d3a718e12"
      },
      {
         "api_url":"https://api.distelli.com/jdoe/servers/3f3843f0-1bd7-7f46-b3db-0800273787c2",
         "html_url":"https://www.distelli.com/jdoe/servers/3f3843f0-1bd7-7f46-b3db-0800273787c2",
         "server_id":"3f3843f0-1bd7-7f46-b3db-0800273787c2"
      }
   ]
}
~~~

#### Documentation

This API call will allow you to list servers based on a tag.

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).


#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### tag_name

The tag name that you are searching for.

> **Note:** If the tag name contains a space it must be URL encoded. Using a **+** works.

#### Pagination

This API command supports pagination and sorting. The following query string parameters can be included:

`max_results` - The number of results to return. The default is 10.

`marker` - Continue pagination from this **marker**.

`order` - Provide sorting values include: **asc** | **desc** for ascending | descending respectively.

For more information on pagination see [Pipelines API pagination](./api.html#pagination).
