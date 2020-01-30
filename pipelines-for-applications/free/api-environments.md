---
layout: default
title: "Pipelines API commands: Environments"
---

## CreateEnv

#### Method

PUT

#### Definition

`https://api.distelli.com/:username/apps/:app_name/envs/:env_name?apiToken=:apiToken`

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
			<td><b>env_name:</b><br>required</td>
			<td><b>String</b><br>The name of the environment you wish to create.</td>
		</tr>
		<tr>
			<td><b>description:</b><br></td>
			<td><b>String</b><br>A description for the created environment.</td>
		</tr>
		<tr>
			<td><b>tags:</b><br></td>
			<td><b>Array of Strings</b><br>Environment tags.</td>
		</tr>
		<tr>
			<td><b>vars:</b><br></td>
			<td><b>Object</b><br>Environment variables.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i -H "Content-Type: application/json" -X PUT "https://api.distelli.com/jdoe/apps/SimpleApp/envs/SA_Beta?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z" -d '{"description": "Beta Environment","tags": ["GOLD","DEV"],"vars": [{"name": "ALEX","value": "\"is cool\""}]}'
~~~

#### Result Format

~~~
{
    "env": {
        "active_release_url": null,
        "active_release_version": null,
        "api_url": "https://api.distelli.com/jdoe/envs/SA_Beta",
        "app_name": "SimpleApp",
        "app_url": "https://api.distelli.com/jdoe/apps/SimpleApp",
        "deployments_url": "https://api.distelli.com/jdoe/envs/SA_Beta/deployments",
        "description": "Beta Environment",
        "html_url": "https://www.distelli.com/jdoe/envs/SA_Beta",
        "last_deployment": null,
        "name": "SA_Beta",
        "owner": "jdoe",
        "server_count": 0,
        "servers_url": "https://api.distelli.com/jdoe/envs/SA_Beta/servers",
        "settings_url": "https://api.distelli.com/jdoe/envs/SA_Beta/settings",
        "tags": [
            "DEV",
            "GOLD"
        ],
        "vars": [
            {
                "name": "ALEX",
                "value": "\"is cool\""
            }
        ]
    }
}
~~~

#### Documentation

This API call will allow you to create a new application environment.

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

#### env_name

Enter a unique name for your new environment.

#### description

Enter a string for your new application environment description.

~~~
{
    "description": "This is a description"
}
~~~

#### tags

Environment tags are an array of strings and must not contain more than three tags.

~~~
{
  "tags": [
    "DEV",
    "GOLD"
  ]
}
~~~


#### vars

Environment variables must be specified as an array of objects. Each object has two pairs; **name** and **value**.

~~~
{
	"vars": [
      {
          "name": "JAVA_HOME",
          "value": "\"/usr/lib/jvm/default-java/jre\""
      },
      {
          "name": "TEST",
          "value": "\"Deploy to test\""
      }
  ]
}
~~~

## DeleteEnv

#### Method

DELETE

#### Definition

`https://api.distelli.com/:username/envs/:env_name?apiToken=:apiToken`

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
			<td><b>String</b><br>The name of the application environment you wish to delete in Pipelines.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i -X DELETE -H "Content-Type: application/json"
"https://api.distelli.com/jdoe/envs/app1-beta?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

#### Result Format

~~~
NO RESULTS ON SUCCESS
~~~

#### Documentation

This API call will allow you to delete an application environment in your Pipelines account.

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

## DeleteEnvVars

#### Method

DELETE

#### Definition

`https://api.distelli.com/:username/envs/:env_name/vars?apiToken=:apiToken`

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
			<td><b>String</b><br>The name of the application environmnet whose variables you wish to delete.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i -X DELETE -H "Content-Type: application/json" "https://api.distelli.com/jdoe/envs/SA_Beta/vars?apiToken=abcdefghijklmnopqrstuvwxyz0123456789"
~~~

#### Result Format

~~~
{
   "vars":[
      {
         "name":"JAVA_HOME",
         "value":"\"/usr/bin/jre\""
      }
   ]
}
~~~

#### Documentation

This API call will delete all environment variables from an application environment.

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

## EditEnvSettings

#### Method 

PATCH

#### Definition

`https://api.distelli.com/:username/envs/:env_name/settings?apiToken=:apiToken`

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
			<td><b>String</b><br>The name of the environment you wish to edit settings.</td>
		</tr>
		<tr>
			<td><b>abort_threshold:</b><br></td>
			<td><b>Integer</b><br>Fail and abort the deploy if <i>abort_threshold</i> servers fail.</td>
		</tr>
		<tr>
			<td><b>auto_deploy:</b><br></td>
			<td><b>Boolean</b><br>Should servers that join this environment via distelli.yml auto deploy the active release.</td>
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
curl -i -H "Content-Type: application/json" -X PATCH "https://api.distelli.com/jdoe/envs/a7/settings?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z" -d '{"auto_deploy": false,"stagger_size": 2,"stagger_delay": 20,"abort_threshold": 1}'
~~~

#### Result Format

~~~
{
   "env_name":"a7",
   "settings":{
      "abort_threshold":1,
      "auto_deploy":false,
      "stagger_delay":20,
      "stagger_size":2
   }
}
~~~

#### Documentation

This API call will allow you to set an environment's settings.

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

#### auto_deploy

Enter a boolean `true | false` whether when servers join this environment via an automated distelli.yml should they deploy the active release.

#### abort_threshold

Enter an integer for your abort threshold. This defines the number of servers to fail in a deploy before the balance of servers are aborted.

~~~
{
    "abort_threshold": 10
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

## EditEnvTags

#### Method

PATCH

#### Definition

`https://api.distelli.com/:username/envs/:env_name/tags?apiToken=:apiToken`

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
			<td><b>tags:</b><br>required</td>
			<td><b>Array of Strings</b><br>A comma-separated list of tags that you wish to add or remove.</td>
		</tr>
		<tr>
			<td><b>action:</b><br>required</td>
			<td><b>String</b><br>Whether you want to <b>add</b> or <b>remove</b> the tag(s) from the environment.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i -X PATCH -H "Content-Type: application/json" "https://api.distelli.com/jdoe/envs/SA_Linux/tags?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z" -d '{"tags": ["TAG1","TAG2"],"action": "add"}'
~~~

#### Result Format

~~~
{
  "tags":[
    "TAG1",
    "TAG2"
  ]
}
~~~

#### Documentation
This API call will allow you to add or remove tags from an application environment in Pipelines.

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

#### tags

Tags is an array of tag(s).

~~~
{
  "tags": [
    "TAG1",
    "TAG2"
  ]
}
~~~

#### action

Action specifies whether you wish to **add** or **remove** the servers from the environment.

~~~
{
    "action": "add"
}
~~~

## GetEnv

#### Method

GET

#### Definition

`https://api.distelli.com/:username/envs/:env_name?apiToken=:apiToken`

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
			<td><b>String</b><br>The name of the application environment you wish to view.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i "https://api.distelli.com/jdoe/envs/SA_Linux?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

#### Result Format

~~~
{
   "env":{
      "active_release_url":"https://api.distelli.com/jdoe/apps/simpleapp/releases/v15",
      "active_release_version":"v15"
      "api_url":"https://api.distelli.com/jdoe/envs/SA_Linux",
      "app_name":"SimpleApp",
      "app_url":"https://api.distelli.com/jdoe/apps/SimpleApp",
      "deployments_url":"https://api.distelli.com/jdoe/envs/SA_Linux/deployments",
      "description":"This is SA_Linux Description.",
      "html_url":"https://www.distelli.com/jdoe/envs/SA_Linux",
      "last_deployment":null,
      "name":"SA_Linux",
      "owner":"jdoe",
      "server_count":2,
      "servers_url":"https://api.distelli.com/jdoe/envs/SA_Linux/servers",
      "settings_url":"https://api.distelli.com/jdoe/envs/SA_Linux/settings",
      "tags":[
         "GOLD",
         "BETA"
      ],
      "vars":[
         {
            "name":"EXAMPLE",
            "value":"\"This is example variable\""
         },
         {
            "name":"EXAMPLE2",
            "value":"\"Another example\""
         }
      ]
   }
}
~~~

#### Documentation

This API call will allow you to view a specific application environment.

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

## GetEnvSettings

#### Method

GET

#### Definition

`https://api.distelli.com/:username/envs/:env_name/settings?apiToken=:apiToken`

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
			<td><b>String</b><br>The name of the environment you wish to edit settings.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i -H "Content-Type: application/json" -X GET "https://api.distelli.com/jdoe/envs/a7/settings?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

#### Result Format

~~~
{
   "env_name":"a7",
   "settings":{
      "abort_threshold":1,
      "auto_deploy":false,
      "stagger_delay":20,
      "stagger_size":2
   }
}
~~~

#### Documentation

This API call will allow you to set an environment's settings.

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

## GetEnvVars

#### Method

GET

#### Definition

`https://api.distelli.com/:username/envs/:env_name/vars?apiToken=:apiToken`

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
			<td><b>String</b><br>The Pipelinesapplication environment name in which you want to view the environment variables.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i "https://api.distelli.com/jdoe/envs/SA_Linux/vars?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

#### Result Format

~~~
{
    "vars": [
        {
            "name": "EXAMPLE",
            "value": "\"This is example variable\""
        },
        {
            "name": "EXAMPLE2",
            "value": "\"Another example\""
        }
    ]
}
~~~

#### Documentation

This API call will allow you to view a specific application environment's environment variables.

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

## ListEnvs

#### Method

GET

#### Definition

`https://api.distelli.com/:username/envs?apiToken=:apiToken`

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
curl -i "https://api.distelli.com/jdoe/envs?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z&max_results=2&order=desc"
~~~

#### Result Format

~~~
{
   "envs":[
      {
         "active_release_url":null,
         "active_release_version":null,
         "api_url":"https://api.distelli.com/jdoe/envs/zzz",
         "app_name":"testapp3",
         "app_url":"https://api.distelli.com/jdoe/apps/testapp3",
         "deployments_url":"https://api.distelli.com/jdoe/envs/zzz/deployments",
         "description":null,
         "html_url":"https://www.distelli.com/jdoe/envs/zzz",
         "last_deployment_url":null,
         "name":"zzz",
         "owner":"jdoe",
         "servers_url":"https://api.distelli.com/jdoe/envs/zzz/servers",
         "settings_url":"https://api.distelli.com/jdoe/envs/zzz/settings",
         "tags":[

         ],
         "vars":[

         ]
      },
      {
         "active_release_url":null,
         "active_release_version":null,
         "api_url":"https://api.distelli.com/jdoe/envs/wordpress-prod",
         "app_name":"wordpress",
         "app_url":"https://api.distelli.com/jdoe/apps/wordpress",
         "deployments_url":"https://api.distelli.com/jdoe/envs/wordpress-prod/deployments",
         "description":null,
         "html_url":"https://www.distelli.com/jdoe/envs/wordpress-prod",
         "last_deployment_url":null,
         "name":"wordpress-prod",
         "owner":"jdoe",
         "servers_url":"https://api.distelli.com/jdoe/envs/wordpress-prod/servers",
         "settings_url":"https://api.distelli.com/jdoe/envs/wordpress-prod/settings",
         "tags":[

         ],
         "vars":[

         ]
      }
   ],
   "marker":"8/vmwqkNB8Fq7IaCuwZ8YA=="
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

## RestartEnv

#### Method

POST

#### Definition

`https://api.distelli.com/:username/envs/:env_name/restart?apiToken=:apiToken`

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
			<td><b>description:</b><br></td>
			<td><b>String</b><br>The description for the deploy.</td>
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
curl -i -X POST -H "Content-Type: application/json" "https://api.distelli.com/jdoe/envs/SA_Linux/restart?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z" -d '{"description": "This is a Description","stagger_size": "7","stagger_delay": "13"}'
~~~

#### Result Format

~~~
{
    "deployment": {
        "api_url": "https://api.distelli.com/jdoe/deployments/37551",
        "app_name": "SimpleApp",
        "app_url": "https://api.distelli.com/jdoe/apps/SimpleApp",
        "deployer": "jdoe",
        "deployment_id": "d-ja1bk2qqao93537f1032",
        "deployment_number": "37551",
        "deployment_state": "InProgress",
        "deployment_time": "2015-09-15T16:44:16Z",
        "deployment_type": "Restart",
        "env_name": "SA_Linux",
        "env_url": "https://api.distelli.com/jdoe/envs/SA_Linux",
        "html_url": "https://www.distelli.com/jdoe/deployments/37551",
        "release_url": null,
        "release_version": null,
        "servers": {
            "done": 0,
            "failed": 0,
            "in_progress": 0,
            "pending": 0,
            "waiting": 2
        },
        "servers_url": "https://api.distelli.com/jdoe/deployments/d-ja1bk2qqao93537f1032/servers",
        "stagger_delay": 13,
        "stagger_size": 7
    }
}
~~~

#### Documentation

This API call will allow you to queue a restart deploy of an application environment.

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
    "stagger_size": 15
}
~~~

## SetEnvActiveRelease

#### Method

PUT

#### Definition

`https://api.distelli.com/:username/envs/:env_name/release/:release_version?apiToken=:apiToken`

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
      <td><b>String</b><br>The application release version you wish to make active for this environment.</td>
    </tr>
	</tbody>
</table>

#### Examples

~~~
curl -i -X PUT -H "Content-Type: application/json" "https://api.distelli.com/jdoe/envs/bash-test/release/v9?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

#### Result Format

~~~
{
   "env":{
      "active_release_url":"https://api.distelli.com/jdoe/apps/BashTestApp/releases/v9",
      "active_release_version":"v9"
      "api_url":"https://api.distelli.com/jdoe/envs/bash-test",
      "app_name":"BashTestApp",
      "app_url":"https://api.distelli.com/jdoe/apps/BashTestApp",
      "deployments_url":"https://api.distelli.com/jdoe/envs/bash-test/deployments",
      "description":null,
      "html_url":"https://www.distelli.com/jdoe/envs/bash-test",
      "last_deployment_url":null,
      "name":"bash-test",
      "owner":"jdoe",
      "server_count":2,
      "servers_url":"https://api.distelli.com/jdoe/envs/bash-test/servers",
      "settings_url":"https://api.distelli.com/jdoe/envs/bash-test/settings",
      "tags":[

      ],
      "vars":[
         {
            "name":"test1",
            "value":"test"
         },
         {
            "name":"test2",
            "value":"test"
         },
      ]
   }
}
~~~

#### Documentation

This API call will allow you to set the current active release for an environment.

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

The application release version that you wish to set as the active release for the environment.

Release ID can be found:

* In the Pipelines web UI [Finding a release ID](./release.html).
* With the Pipelines API ListReleases command.

## SetEnvVars

#### Method

PUT

#### Definition

`https://api.distelli.com/:username/envs/:env_name/vars?apiToken=:apiToken`

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
			<td><b>vars:</b><br>required</td>
			<td><b>Object</b><br>The environment environment variables..</td>
		</tr>

	</tbody>
</table>

#### Examples

~~~
curl -i -X PUT -H "Content-Type: application/json" "https://api.distelli.com/jdoe/envs/SA_Linux/vars?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z" -d '{"vars": [{"name": "NAME1","value": "Value1"},{"name": "VARIABLE2","value": "Var2 value"}]}'
~~~

#### Result Format

~~~
{
   "vars":[
      {
         "name":"NAME1",
         "value":"Value1"
      },
      {
         "name":"VARIABLE2",
         "value":"Var2 value"
      }
   ]
}
~~~

#### Documentation

This API call will allow you to set an application environments environment variables. The order of the environment variables is relevant.

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

#### vars

Specify environment variables as an array of objects. Each object has two pairs, **name** and **value**.

~~~
{
    "vars": [
        {
            "name": "EXAMPLE",
            "value": "\"This is example variable\""
        },
        {
            "name": "JAVA_HOME",
            "value": "\"/usr/local/jre\""
        }
    ]
}
~~~

## TerminateEnv

#### Method

POST

#### Definition

`https://api.distelli.com/:username/envs/:env_name/terminate?apiToken=:apiToken`

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
			<td><b>description:</b><br></td>
			<td><b>String</b><br>The description for the deploy.</td>
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
curl -i -X POST -H "Content-Type: application/json" "https://api.distelli.com/jdoe/envs/SA_Linux/terminate?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z" -d '{"description": "This is a Description","stagger_size": "2","stagger_delay": "29"}'
~~~

#### Result Format

~~~
{
    "deployment": {
        "api_url": "https://api.distelli.com/jdoe/deployments/37553",
        "app_name": "SimpleApp",
        "app_url": "https://api.distelli.com/jdoe/apps/SimpleApp",
        "deployer": "jdoe",
        "deployment_id": "d-i3n5u1v9g2a6bf6f77cd",
        "deployment_number": "37553",
        "deployment_state": "InProgress",
        "deployment_time": "2015-09-15T16:50:12Z",
        "deployment_type": "Terminate",
        "env_name": "SA_Linux",
        "env_url": "https://api.distelli.com/jdoe/envs/SA_Linux",
        "html_url": "https://www.distelli.com/jdoe/deployments/37553",
        "release_url": null,
        "release_version": null,
        "servers": {
            "done": 0,
            "failed": 0,
            "in_progress": 0,
            "pending": 0,
            "waiting": 2
        },
        "servers_url": "https://api.distelli.com/jdoe/deployments/d-i3n5u1v9g2a6bf6f77cd/servers",
        "stagger_delay": 29,
        "stagger_size": 2
    }
}
~~~

#### Documentation

This API call will allow you to enqueue a restart deploy of an application in an application environment.

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
    "stagger_size": 15
}
~~~

