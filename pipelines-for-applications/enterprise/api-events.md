---
layout: default
title: "Pipelines API commands: Events"
---

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
<p><b>*</b> Important. If you set the event to any of these terminal *states, you can no longer update  the event. It has become an immutable historical object.</p>

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

This API call will allow the creation of a build event for an application in Pipelines.

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

What CI tool is providing the build. Typically this is the service making the API call to Pipelines. Supported providers are Jenkins, CircleCI, Travis CI, Codeship, and Azure DevOps.

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

## CreatePullRequestEvent

#### Method

PUT

#### Definition

`https://api.distelli.com/:username/apps/:app_name/events/pullRequestEvent?apiToken=:apiToken`

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
      <td><b>String</b><br>The name of the application you wish to create a pull request event.</td>
    </tr>
		<tr>
			<td><b>repo_url:</b><br></td>
			<td><b>String</b><br>The full URL to the repository making the pull request.</td>
		</tr>
		<tr>
			<td><b>commit_url:</b><br></td>
			<td><b>String</b><br>The full URL to the commit making the pull request.</td>
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
			<td><b>String</b><br>The committers user name.</td>
		</tr>
		<tr>
			<td><b>author_name:</b><br></td>
			<td><b>String</b><br>The committers full name.</td>
		</tr>
		<tr>
			<td><b>commit_msg:</b><br></td>
			<td><b>String</b><br>The commit message.</td>
		</tr>
		<tr>
			<td><b>commit_id:</b><br></td>
			<td><b>String</b><br>The commit id, typically a SHA.</td>
		</tr>
		<tr>
			<td><b>branch:</b><br></td>
			<td><b>String</b><br>The repository branch making the pull request.</td>
		</tr>

	</tbody>
</table>

#### Examples

~~~
curl -v -H "Content-Type: application/json" -X PUT "https://api-beta.distelli.com/jdoe/apps/example-node/events/pullRequestEvent?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z" -d '{"repo_url":"http
s://github.example.com/jdoe/example-node", "commit_url":"https://github.example.com/jdoe/example-node/commits/1234567890123456789012345678901234567890", "repo_owner":"jdoe", "repo_name":"example-node", "re
po_provider":"github", "author_username":"jdoe", "author_name":"John Doe", "commit_msg":"Just a Test", "commit_id":"1234567890123456789012345678901234567890", "branch":"master"}'
~~~

#### Result Format

~~~
{
    "event_id":"10400"
}
~~~

#### Documentation

This API call will allow the creation of a pull request event for an application in Pipelines.

Typically this call will be made from a third-party build system to identify pull request information in relation to pushing releases to Pipelinesfor use in Pipelines. This call will allow you identify useful information to ensure you have end-to-end visability of who did what when.

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

#### repo_url

A URL that links to the repository making the pull request.

#### commit_url

A URL that links to the repository commit information that is making the pull request.

#### repo_owner

The pull request repository owner's user name.

#### repo_name

The repository owner's full name.

#### repo_provider

The hosting service of repository.

#### author_username

The committer's user name.

#### author_name

The committer's full name.

#### commit_msg

The commit message for the commit making the pull request. This is prominent in Pipelines.

#### commit_id

The commit id, typically a SHA.

#### branch

The repository branch making the pull request.

## CreatePushEvent

#### Method

PUT

#### Definition

`https://api.distelli.com/:username/apps/:app_name/events/pushEvent?apiToken=:apiToken`

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
      <td><b>String</b><br>The name of the application you wish to create a push event.</td>
    </tr>
		<tr>
			<td><b>repo_url:</b><br></td>
			<td><b>String</b><br>The full URL to the repository being pushed.</td>
		</tr>
		<tr>
			<td><b>commit_url:</b><br></td>
			<td><b>String</b><br>The full URL to the commit being pushed.</td>
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
			<td><b>String</b><br>The repository branch being pushed.</td>
		</tr>

	</tbody>
</table>

#### Examples

~~~
curl -v -H "Content-Type: application/json" -X PUT "https://api-beta.distelli.com/jdoe/apps/example-node/events/pushEvent?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z" -d '{"repo_url":"http
s://github.example.com/jdoe/example-node", "commit_url":"https://github.example.com/jdoe/example-node/commits/1234567890123456789012345678901234567890", "repo_owner":"jdoe", "repo_name":"example-node", "re
po_provider":"github", "author_username":"jdoe", "author_name":"John Doe", "commit_msg":"Just a Test", "commit_id":"1234567890123456789012345678901234567890", "branch":"master"}'
~~~

#### Result Format

~~~
{
    "event_id":"10100"
}
~~~

#### Documentation

This API call will allow the creation of a push event for an application in Pipelines.

Typically this call will be made from a third-party build system to identify push information in relation to pushing releases to Pipelinesfor use in Pipelines. This call will allow you identify useful information to ensure you have end-to-end visability of who did what when.

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

#### repo_url

A URL that links to the repository being pushed.

#### commit_url

A URL that links to the repository commit information that is being pushed.

#### repo_owner

The repository owner's user name.

#### repo_name

The repository owner's full name.

#### repo_provider

The hosting service of the repository.

#### author_username

The committer's user name.

#### author_name

The committer's full name.

#### commit_msg

The commit message for the commit being pushed. This is prominent in Pipelines.

#### commit_id

The commit id, typically a SHA.

#### branch

The repository branch being pushed.

## ListEvents

#### Method

GET

#### Definition

`https://api.distelli.com/:username/apps/:app_name/events?apiToken=:apiToken`

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
      <td><b>String</b><br>The name of the application you wish to list releases.</td>
    </tr>
	</tbody>
</table>

#### Examples

~~~
curl -i "https://api.distelli.com/jdoe/apps/gobuilddeploy/events?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z&max_results=2&order=desc&filter_name=EventType&filter_event_type=build"
~~~

#### Result Format

~~~
{
  "app_name": "example-node",
  "events": [
    {
      "app_name": "example-node",
      "event_id": 2000,
      "event_time": "2016-08-30T22:31:03.723Z",
      "event_type": "DEPLOY",
      "author_username": null,
      "auto_deploy": true,
      "branch": null,
      "commit_id": "05169ab2384e8cd991cfdf4e61f9642ccf600758",
      "commit_msg": "build2\n",
      "commit_url": null,
      "deployment_id": "d-h6kpu8hfh45ede99308a",
      "deployment_number": "107557",
      "deployment_status": "Failed",
      "deployment_type": "Deploy",
      "env_name": "ej-dev",
      "parent_event_id": null,
      "release_version": "v5",
      "repo_name": null,
      "repo_owner": null,
      "repo_provider": null,
      "repo_url": null
    },
    {
      "app_name": "example-node",
      "event_id": 1900,
      "event_time": "2016-08-30T22:31:03.445Z",
      "event_type": "RELEASE",
      "author_name": null,
      "author_username": null,
      "branch": null,
      "build_id": null,
      "commit_id": "05169ab2384e8cd991cfdf4e61f9642ccf600758",
      "commit_msg": null,
      "commit_url": null,
      "parent_event_id": null,
      "release_version": "v5",
      "repo_name": null,
      "repo_owner": null,
      "repo_provider": null,
      "repo_url": null
    },
    {
      "app_name": "example-node",
      "event_id": 1801,
      "event_time": "2016-08-30T22:30:44.770Z",
      "event_type": "BUILD",
      "author_name": "doct15",
      "author_username": "doct15",
      "auto_build": null,
      "branch": "origin/master",
      "build_end": "2016-08-30T22:31:11.000Z",
      "build_id": "96",
      "build_provider": "node",
      "build_start": "2016-08-30T22:30:52.000Z",
      "build_status": "Success",
      "build_url": "http://54.210.169.27:8080/job/example-node/96/",
      "commit_id": "05169ab2384e8cd991cfdf4e61f9642ccf600758",
      "commit_msg": "NoChangeMessage",
      "commit_url": "https://github.com/doct15/example-node",
      "parent_event_id": "1800",
      "release_version": "v5",
      "repo_name": null,
      "repo_owner": null,
      "repo_provider": null,
      "repo_url": "https://github.com/doct15/example-node"
    },
    {
      "app_name": "example-node",
      "event_id": 1800,
      "event_time": "2016-08-30T22:30:44.439Z",
      "event_type": "PUSH",
      "author_name": "doct15",
      "author_username": "doct15",
      "branch": "origin/master",
      "commit_id": "05169ab2384e8cd991cfdf4e61f9642ccf600758",
      "commit_msg": "NoChangeMessage",
      "commit_url": "https://github.com/doct15/example-node",
      "repo_name": null,
      "repo_owner": null,
      "repo_provider": null,
      "repo_url": "https://github.com/doct15/example-node"
    }]
}
~~~

#### Documentation

This API call will allow you to list all events.

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

#### Filtering

This API command supports filtering the results based on the following:

`Branch` - This will filter the list based on a Branch name.

Example Branch Filter

~~~
curl -i "https://api.distelli.com/jdoe/apps/gobuilddeploy/events?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z&filter_name=Branch&filter_branch=master"
~~~

`Env` - This will filter the list based on an Environment name in Pipelines. The Env must belong to the app_name Application.

Example Env Filter

~~~
curl -i "https://api.distelli.com/jdoe/apps/gobuilddeploy/events?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z&filter_name=Env&filter_env=gbd_dev1"
~~~


`EventType` - This will filter the list based on Event Types. Event typs include:
  - Build       - Build events
  - Deploy      - Pipelines deploy events
  - Dockerimage - [in development]
  - Pullrequest - Repository 
  - Push        - Repository push events
  - Release     - Pipelines release creation events

Example EventType Filter

~~~
curl -i "https://api.distelli.com/jdoe/apps/gobuilddeploy/events?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z&filter_name=EventType&filter_event_type=deploy"
~~~


`BranchAndEventType`

Example Branch and EventType Filter

~~~
curl -i "https://api.distelli.com/jdoe/apps/gobuilddeploy/events?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z&filter_name=BranchAndEventType&filter_branch=master&filter_event_type=deploy"
~~~


`EnvAndBranch`

Example Env and Branch Filter

~~~
curl -i "https://api.distelli.com/jdoe/apps/gobuilddeploy/events?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z&filter_name=EnvAndBranch&filter_branch=master&filter_env=gbd_dev1"
~~~

#### Pagination

This API command supports pagination and sorting. The following query string parameters can be included:

`max_results` - The number of results to return. The default is 10.

`marker` - Continue pagination from this **marker**.

`order` - Provide sorting values include: **asc** | **desc** for ascending | descending respectively.

For more information on pagination see [Pipelines API pagination](./api.html#pagination).

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

