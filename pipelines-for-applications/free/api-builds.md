---
layout: default
title: "Pipelines API commands: Builds"
---

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

## CancelBuild

#### Method

POST

#### Definition

`https://api.distelli.com/:username/builds/:build_num/cancel?apiToken=:apiToken`

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
			<td><b>build_num:</b><br>required</td>
			<td><b>String</b><br>The build number you wish to cancel.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i -X POST -H "Content-Type: application/json" "https://api.distelli.com/jdoe/builds/7191/cancel?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

#### Result Format

~~~
{
  "build": {
    "api_url": "https://api.distelli.com/jdoe/builds/7191",
    "app_name": "rubyubunturbenvapp",
    "build_num": "7191",
    "build_server": null,
    "cancelled_by": "jdoe",
    "cancelling": false,
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
    "deployments": [
      
    ],
    "end_time": "2015-11-23T21:07:11Z",
    "html_url": "https://www.distelli.com/jdoe/builds/7191",
    "release_url": "https://api.distelli.com/jdoe/apps/rubyubunturbenvapp/releases/v55",
    "release_version": "v55",
    "start_time": "2015-11-23T21:06:62Z",
    "status": "Success"
  }
}
~~~

#### Documentation

This API call will allow you to cancel an existing build of an application in Pipelines.

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).


#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### build_num

Build numbers can be found:

* In the Pipelines web UI [Viewing builds](./build.html).
* With the Pipelines API ListBuilds command.
* With the Pipelines API ListAppBuilds command.

## CreateBuildEvent

#### Method

PUT

#### Definition

`https://api.distelli.com/:username/apps/:app_name/events/buildEvent?apiToken=:apiToken`

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
      <td><b>String</b><br>The name of the application you wish to create a build event.</td>
    </tr>
		<tr>
			<td><b>build_status:</b><br></td>
			<td><b>String</b><br>Set the current build status. This can be updated with an UpdateBuildEvent API call.<br>
Possible Values:
<ul>
<li>Waiting    - Waiting to be queued.</li>
<li>Queued     - In the queue to be built.</li>
<li>Running    - The build is currently running.</li>
<li>*Success   - The build succeeded.</li>
<li>*Failed    - The build failed.</li>
<li>*TimedOut  - The build timed out.</li>
<li>*Cancelled - The build was canceled.</li>
</ul>

<p><b>*</b> Important. If you set the event to any of these terminal *states, you can no longer update the event. It has become an immutable historical object.</p>

      </td>
		</tr>
		<tr>
			<td><b>build_start:</b><br></td>
			<td><b>String</b><br>The build start date and time in ISO 8601 format.<br>
			Example: <code>2016-08-15T22:22:22.111Z</code></td>
		</tr>
		<tr>
			<td><b>build_end:</b><br></td>
			<td><b>String</b><br>The build end date and time in ISO 8601 format.<br>Ensure this is set before setting <b>build_status</b> to a terminal condition.</td>
		</tr>
		<tr>
			<td><b>auto_build:</b><br></td>
			<td><b>Boolean</b><br>Was this a CI auto build? <b>true</b> or <b>false</b>.</td>
		</tr>
		<tr>
			<td><b>build_id:</b><br></td>
			<td><b>String</b><br>The build identifier.</td>
		</tr>
		<tr>
			<td><b>build_provider:</b><br></td>
			<td><b>String</b><br>The CI tool providing the build.<br>
Possible Values:
<ul>
<li>Jenkins</li>
<li>CircleCI</li>
<li>TravisCI</li>
<li>Codeship</li>
<li>AzureDevops</li>
</ul>
			</td>
		</tr>
		<tr>
			<td><b>build_url:</b><br></td>
			<td><b>String</b><br>The full URL to the build.</td>
		</tr>
		<tr>
			<td><b>repo_url:</b><br></td>
			<td><b>String</b><br>The full URL to the repository being built.</td>
		</tr>
		<tr>
			<td><b>commit_url:</b><br></td>
			<td><b>String</b><br>The full URL to the commit being built.</td>
		</tr>
		<tr>
			<td><b>repo_owner:</b><br></td>
			<td><b>String</b><br>The repository owner user name.</td>
		</tr>
		<tr>
			<td><b>repo_name:</b><br></td>
			<td><b>String</b><br>The repository owner full name.</td>
		</tr>
		<tr>
			<td><b>repo_provider:</b><br></td>
			<td><b>String</b><br>The repository provider.<br>
Possible Values:
<ul>
<li>bitbucket</li>
<li>bitbucketserver</li>
<li>github</li>
<li>githubenterprise</li>
<li>gitlab</li>
<li>azuredevops</li>
</ul>
      </td>
		</tr>
		<tr>
			<td><b>author_username:</b><br></td>
			<td><b>String</b><br>The committer's user name.</td>
		</tr>
		<tr>
			<td><b>author_name:</b><br></td>
			<td><b>String</b><br>The committer's full name.</td>
		</tr>
		<tr>
			<td><b>commit_msg:</b><br></td>
			<td><b>String</b><br>The commit message.</td>
		</tr>
		<tr>
			<td><b>commit_id:</b><br></td>
			<td><b>String</b><br>The commit ID, typically a SHA.</td>
		</tr>
		<tr>
			<td><b>branch:</b><br></td>
			<td><b>String</b><br>The repository branch being built.</td>
		</tr>
		<tr>
			<td><b>parent_event_id:</b><br></td>
			<td><b>String</b><br>The parent event ID that triggered this event.</td>
		</tr>

	</tbody>
</table>

#### Examples

~~~
curl -i -X PUT -H "Content-Type: application/json" "https://api.distelli.com/jdoe/apps/example-node/events/buildEvent?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z" -d '{"build_status":"Running", "build_start":"2016-08-15T22:22:22.111Z", "auto_build":true, "build_id":"J23465", "build_provider":"jenkins", "build_url":"http://jenkins.example.com", "repo_url":"https://github.example.com/jdoe/example-node", "commit_url":"https://github.example.com/jdoe/example-node/commits/1234567890123456789012345678901234567890", "repo_owner":"jdoe", "repo_name":"example-node", "repo_provider":"github", "author_username":"jdoe", "author_name":"John Doe", "commit_msg":"Just a Test", "commit_id":"1234567890123456789012345678901234567890", "branch":"master"}'
~~~

#### Result Format

~~~
{
    "event_id":"9100"
}
~~~

#### Documentation

This API call will allow the creation of a build event for an application.

Typically this call will be made from a third party build system to identify build information in relation to pushing releases to Pipelines for use in Pipelines. This call will allow you identify useful information to ensure you have end-to-end visability of who did what when, in Pipelines.

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

#### build_status

This is the current status of the build when the API call is made. This will be shown in Pipelines.

#### build_start

This is the start time of the build. Pipelineswill use this, along with build_end, to determine how long the build took. This information is available in Pipelines.

#### build_end

This is the end time of the build. Pipelineswill use this, along with build_start, to determine how long the build took. This information is available in Pipelines.

#### build_provider

What CI tool is providing the build. Typically this is the service making the API call to Pipelines.

#### build_url

A URL that links to the build. This link is available in Pipelinesso that users can link to the build and see the status and logs.

#### repo_url

A URL that links to the repository being built.

#### commit_url

A URL that links to the repository commit information that is being built.

#### repo_owner

The repository owner user name.

#### repo_name

The repository owner full name.

#### repo_provider

The hosting service of repository.

#### author_username

The committers user name.

#### author_name

The committers full name.

#### commit_msg

The commit message for the commit being built. This is prominent in Pipelines.

#### commit_id

The commit id, typically a SHA.

#### branch

The repository branch being built.

#### parent_event_id

The parent event ID that is directly tied to this event. Because this event is build specific, the only parent it should have is a repository push/commit (see the CreatePushEvent command) or pull request (see the CreatePullRequestEvent command).

## DeleteApp

#### Method

DELETE

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
			<td><b>String</b><br>The name of the application you wish to delete in Pipelines.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i -X DELETE -H "Content-Type: application/json" "https://api.distelli.com/jdoe/apps/NewApp1?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

#### Result Format

~~~
NO RESULTS ON SUCCESS
~~~

#### Documentation

This API call will allow you to delete an application in your Pipelinesaccount.

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).


#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### app_name

Enter the name of the application you wish to delete.

Application name can be found:

* In the Pipelines web UI [Viewing an application](./application-manage.html).
* With the Pipelines API ListApps command.

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

## GetBuild

#### Method

GET

#### Definition

`https://api.distelli.com/:username/builds/:build_num?apiToken=:apiToken`

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
			<td><b>build_num:</b><br>required</td>
			<td><b>String</b><br>The build number, in Pipelines, you wish to view.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i "https://api.distelli.com/jdoe/builds/1622?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

#### Result Format

~~~
{
    "build": {
        "api_url": "https://api.distelli.com/jdoe/builds/1622",
        "app_name": "gobuilddeploy",
        "build_num": "1622",
        "build_server": null,
        "commit": {
            "branch": "master",
            "commit_id": "0dbda6e34f9382951b9b1ea8959fb210634a5ac2",
            "committer": "doct15",
            "date": "2015-09-11T19:08:00Z",
            "msg": "Trigger build",
            "repo_name": "gobuilddeploy",
            "repo_owner": "doct15",
            "url": "https://github.com/doct15/gobuilddeploy/commit/0dbda6e34f9382951b9b1ea8959fb210634a5ac2"
        },
        "create_time": "2015-09-11T19:08:88Z",
        "dependencies": null,
        "deployments": [
            "https://api.distelli.com/jdoe/deployments/d-z3n28gh08vd5247740e0"
        ],
        "end_time": "2015-09-11T19:09:06Z",
        "html_url": "https://www.distelli.com/jdoe/builds/1622",
        "release_url": null,
        "release_version": null,
        "start_time": "2015-09-11T19:08:04Z",
        "status": "Success"
    }
}
~~~

#### Documentation

This API call will allow you to view a specific build.

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).


#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### build_num

Build numbers can be found:

* In the Pipelines web UI [Viewing builds](./build.html).
* With the Pipelines API ListBuilds command.
* With the Pipelines API ListAppBuilds command.

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

## ListBuilds

#### Method

GET

#### Definition

`https://api.distelli.com/:username/builds?apiToken=:apiToken`

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
curl -i "https://api.distelli.com/jdoe/builds?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z&max_results=2&order=desc"
~~~

#### Result Format

~~~
{
   "builds":[
      {
         "api_url":"https://api.distelli.com/jdoe/builds/1217",
         "app_name":"wordpress",
         "build_num":"1217",
         "build_server":null,
         "commit":{
            "branch":"master",
            "commit_id":"366bca7b87e8a16c8b886546bddef5251da5c3b1",
            "committer":"doct15",
            "date":"2015-08-31T18:01:00Z",
            "msg":"Test",
            "repo_name":"wordpress",
            "repo_owner":"doct15",
            "url":"https://github.com/doct15/wordpress/commit/366bca7b87e8a16c8b886546bddef5251da5c3b1"
         },
         "create_time":"2015-08-31T18:01:46Z",
         "dependencies":null,
         "deployments":[

         ],
         "end_time":"2015-08-31T18:01:09Z",
         "html_url":"https://www.distelli.com/jdoe/builds/1217",
         "release_url":null,
         "release_version":null,
         "start_time":"2015-08-31T18:01:83Z",
         "status":"Success"
      },
      {
         "api_url":"https://api.distelli.com/jdoe/builds/1164",
         "app_name":"wordpress",
         "build_num":"1164",
         "build_server":null,
         "commit":{
            "branch":"master",
            "commit_id":"76cfd4e60e9b91078bdb2de340f93a01a9d5a93b",
            "committer":"doct15",
            "date":null,
            "msg":"Test",
            "repo_name":"wordpress",
            "repo_owner":"doct15",
            "url":"https://github.com/doct15/wordpress/commit/76cfd4e60e9b91078bdb2de340f93a01a9d5a93b"
         },
         "create_time":"2015-08-28T00:15:76Z",
         "dependencies":null,
         "deployments":[

         ],
         "end_time":"2015-08-28T00:15:19Z",
         "html_url":"https://www.distelli.com/jdoe/builds/1164",
         "release_url":null,
         "release_version":null,
         "start_time":"2015-08-28T00:15:80Z",
         "status":"Success"
      }
   ],
   "marker":"pv/bPM7+rjnaiWQ6e+BrYaYvRbMEEaMY80DoqY8Si/Ate8Nw2W6v68cDmFRuybgb9zqebCAeunx/ptZ1iZ5QpHeffn775F4f"
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

#### Pagination

This API command supports pagination and sorting. The following query string parameters can be included:

`max_results` - The number of results to return. The default is 10.

`marker` - Continue pagination from this **marker**.

`order` - Provide sorting values include: **asc** | **desc** for ascending | descending respectively.

For more information on pagination see [Pipelines API pagination](./api.html#pagination).

## RestartBuild

#### Method

POST

#### Definition

`https://api.distelli.com/:username/builds/:build_num/restart?apiToken=:apiToken`

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
			<td><b>build_num:</b><br>required</td>
			<td><b>String</b><br>The build number you wish to cancel.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i -X POST -H "Content-Type: application/json" "https://api.distelli.com/jdoe/builds/7191/restart?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
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

This API call will allow you to restart an existing build of an application in Pipelines.

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).


#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### build_num

Build numbers can be found:

* In the Pipelines web UI [Viewing builds](./build.html).
* With the Pipelines API ListBuilds command.
* With the Pipelines API ListAppBuilds command.

## UpdateBuildEvent

#### Method

POST

#### Definition

`https://api.distelli.com/:username/apps/:app_name/events/:eventId?apiToken=:apiToken`

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
      <td><b>String</b><br>The name of the application you wish to edit a build event.</td>
    </tr>
    <tr>
      <td><b>eventId:</b><br>required</td>
      <td><b>String</b><br>The Pipelines build event ID you wish to update.</td>
    </tr>
		<tr>
			<td><b>build_end:</b><br></td>
			<td><b>String</b><br>The build end date and time in ISO 8601 format.<br>
						Example: <code>2016-08-15T22:22:22.111Z</code><br>
						Ensure this is set before setting <b>build_status</b> to a terminal condition.</td>
		</tr>
		<tr>
			<td><b>build_status:</b><br></td>
			<td><b>String</b><br>Set the current build status. This can be updated with an UpdateBuildEvent call.<br>
Possible Values:
<ul>
<li>Waiting    - Waiting to be queued.</li>
<li>Queued     - In the queue to be built.</li>
<li>Running    - The build is currently running.</li>
<li><b>*</b> Success   - The build succeeded.</li>
<li><b>*</b> Failed    - The build failed.</li>
<li><b>*</b> TimedOut  - The build timed out.</li>
<li><b>*</b> Cancelled - The build was canceled.</li>
</ul>

<p><b>*</b> Important. If you set the event to any of these terminal *states, you can no longer update any value on the event. It has become an immutable historical object.</p>

      </td>
		</tr>

		<tr>
			<td><b>release_version:</b><br></td>
			<td><b>String</b><br>The specific Pipelinesrelease version that was created as a result of a successful <code>distelli push</code>.</td>
		</tr>

	</tbody>
</table>

#### Examples

~~~
curl -v -H "Content-Type: application/json" -X POST "https://api-beta.distelli.com/jdoe/apps/example-node/events/9300?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z" -d '{"build_status":"Success","build_end":"2016-08-17T22:22:22.111Z","release_version":"v123"}'
~~~

#### Result Format

~~~
{
   "event":{
      "app_name":"example-node",
      "event_id":1801,
      "event_time":"2016-08-30T22:30:44.770Z",
      "event_type":"BUILD",
      "author_name":"doct15",
      "author_username":"doct15",
      "auto_build":null,
      "branch":"origin/master",
      "build_end":"2016-08-30T22:31:11.000Z",
      "build_id":"96",
      "build_provider":"node",
      "build_start":"2016-08-30T22:30:52.000Z",
      "build_status":"Success",
      "build_url":"http://54.210.169.27:8080/job/example-node/96/",
      "commit_id":"05169ab2384e8cd991cfdf4e61f9642ccf600758",
      "commit_msg":"NoChangeMessage",
      "commit_url":"https://github.com/doct15/example-node",
      "parent_event_id":"1800",
      "release_version":"v5",
      "repo_name":null,
      "repo_owner":null,
      "repo_provider":null,
      "repo_url":"https://github.com/doct15/example-node"
   }
}
~~~

#### Documentation

This API call will allow you to add or remove server capabilities.

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

Enter a Boolean `true | false` to mark this server as a build server or not.
