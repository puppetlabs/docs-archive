---
layout: default
title: "Pipelines API commands: Releases"
---

## EditReleaseTags

#### Method

PATCH

#### Definition

`https://api.distelli.com/:username/apps/:app_name/releases/:release_version/tags?apiToken=:apiToken`

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
		<tr>
			<td><b>release_version:</b><br>required</td>
			<td><b>String</b><br>The application release version you wish to view.</td>
		</tr>
		<tr>
			<td><b>tags:</b><br>required</td>
			<td><b>Array of Strings</b><br>A comma-separated list of tags that you wish to add or remove.</td>
		</tr>
		<tr>
			<td><b>action:</b><br>required</td>
			<td><b>String</b><br>Whether you want to <b>add</b> or <b>remove</b> the tag(s) from the release.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i -X PATCH -H "Content-Type: application/json" "https://api.distelli.com/jdoe/apps/BlogEngine/release/v15/tags?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z" -d '{"tags": ["TAG1","TAG2"],"action": "add"}'
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

This API call will allow you to add or remove tags from an application release in Pipelines.

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

#### release_version

Release ID can be found:

* In the Pipelines web UI [Finding a release ID](./release.html).
* With the Pipelines API ListReleases command.

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

## GetRelease

#### Method

GET

#### Definition

`https://api.distelli.com/:username/apps/:app_name/releases/:release_version?apiToken=:apiToken`

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
			<td><b>String</b><br>The name of the application whose release you wish to view.</td>
		</tr>
		<tr>
			<td><b>release_version:</b><br>required</td>
			<td><b>String</b><br>The application release version you wish to view.</td>
		</tr>
	</tbody>
</table>

#### Examples

~~~
curl -i "https://api.distelli.com/jdoe/apps/gobuilddeploy/releases/v31?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

#### Result Format

~~~
{
    "release": {
        "api_url": "https://api.distelli.com/jdoe/apps/gobuilddeploy/releases/v31",
        "app_name": "gobuilddeploy",
        "build_num": "2038",
        "build_url": "https://api.distelli.com/jdoe/builds/2038",
        "commit": {
            "branch": "master",
            "commit_id": "1a2afff732d41bb7e06250f2ab8a19739649043c",
            "committer": "doct15",
            "date": "2015-09-17T20:41:00Z",
            "msg": "Test",
            "repo_name": "gobuilddeploy",
            "repo_owner": "doct15",
            "url": null
        },
        "created": "2015-09-17T21:30:28Z",
        "created_by": "jdoe",
        "description": "Test",
        "html_url": "https://www.distelli.com/jdoe/releases/a-i3tqp7qidu0c2cda989f",
        "release_version": "v31",
        "tags": null
    }
}
~~~

#### Documentation

This API call will allow you to get information on a Puppet Pipelines for Applications release.

#### username

Your Pipelines username can be found:

* In the Pipelines web UI [Finding Your Pipelines username](./account.html).


#### apiToken

Your Pipelines API token can be found:

* In the Pipelines web UI [Finding your token](./api-token.html).

#### app_name

Enter the name of the application you wish to look at its releases.

Application name can be found:

* In the Pipelines web UI [Viewing an application](./application-manage.html).
* With the Pipelines API ListApps command.

#### release_id

Release ID can be found:

* In the Pipelines web UI [Finding a release ID](./release.html).
* With the Pipelines API ListReleases command.

## ListReleases

#### Method

GET

#### Definition

`https://api.distelli.com/:username/apps/:app_name/releases?apiToken=:apiToken`

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
curl -i "https://api.distelli.com/jdoe/apps/gobuilddeploy/releases?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z&max_results=2&order=desc"
~~~

#### Result Format

~~~
{
   "marker":"K2K2Wdlkc4MNvJOgMfSnjCzV/9e1botJK4/WV9GYiGY=",
   "releases":[
      {
         "api_url":"https://api.distelli.com/jdoe/apps/gobuilddeploy/releases/v31",
         "app_name":"gobuilddeploy",
         "build_num":"2038",
         "build_url":"https://api.distelli.com/jdoe/builds/2038",
         "commit":{
            "branch":"master",
            "commit_id":"1a2afff732d41bb7e06250f2ab8a19739649043c",
            "committer":"doct15",
            "date":"2015-09-17T20:41:00Z",
            "msg":"Test",
            "repo_name":"gobuilddeploy",
            "repo_owner":"doct15",
            "url":null
         },
         "created":"2015-09-17T21:30:28Z",
         "created_by":"jdoe",
         "description":"Test",
         "html_url":"https://www.distelli.com/jdoe/releases/a-i3tqp7qidu0c2cda989f",
         "release_version":"v31",
         "tags":null
      },
      {
         "api_url":"https://api.distelli.com/jdoe/apps/gobuilddeploy/releases/v30",
         "app_name":"gobuilddeploy",
         "build_num":"2031",
         "build_url":"https://api.distelli.com/jdoe/builds/2031",
         "commit":{
            "branch":null,
            "commit_id":"cfefb1562bc63b22bc11ba7dec6e68f16406510f",
            "committer":"doct15",
            "date":"2015-09-17T20:46:00Z",
            "msg":"Test",
            "repo_name":"gobuilddeploy",
            "repo_owner":"doct15",
            "url":null
         },
         "created":"2015-09-17T20:47:24Z",
         "created_by":"jdoe",
         "description":"Test",
         "html_url":"https://www.distelli.com/jdoe/releases/a-monbz294hfecaab1100",
         "release_version":"v30",
         "tags":null
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
