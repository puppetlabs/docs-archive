---
layout: default
title: "Events API"
--- 

You can use the Pipelines for Containers events API to list the projects associated with your account. You can also use it to list events for each project.

## Authentication

To authenticate each request, use the API token associated with your account. 

> **Tip**: To manage your tokens, go to **Settings** > **API**. 

For each API request, include your account name and your API token. For example, a typical request might look like this:

```curl "https://api.distelli.com/<YOUR_ACCOUNT_NAME>/<ENDPOINT>?apiToken=<YOUR_API_TOKEN>"```

## GET `/projects`  
Use `/projects` to return all projects associated with an account. 

### Request format
The body of the request must include your account name and authentication token.

```curl "https://api.distelli.com/<YOUR_ACCOUNT_NAME>/projects?apiToken=<YOUR_API_TOKEN>"```

### Response format
The `/projects` endpoint responds with the list of project names and descriptions associated with your account.

A typical response might look like this:

```
{
  "Projects":[
    {
      "description":"Deployment for getting started guide",
      "name":"Guide"
    },
    {
      "description":"Deployment of Puppet Container Registry",
      "name":"PCR"
    },
    {
      "description":"Testing builds with Pipelines agent",
      "name":"testagent"
    },
    {
      "description":"Wordpress and MySQL containers for k8s",
      "name":"Wordpress"
    }
  ]
}
```

## GET `/projects/<YOUR_PROJECT>/events`
Use `projects/<YOUR_PROJECT>/events` to list all events for a particular project 

### Request format
The body of the request must include your account name and authentication token.

```curl "https://api.distelli.com/<YOUR_ACCOUNT_NAME>/projects/<YOUR_PROJECT_NAME>/events?apiToken=<YOUR_API_TOKEN>"```

### Response format

<table>
  <tr>
    <th>Parameter</th>
    <th>Definition</th>
    <th>Values</th>
  </tr>
  <tr>
    <td><code>PUSH</code></td>
    <td>Lists all push events associated with the specified project.</td>
    <td> 
      <ul>
       <li>author_name</li>
       <li>author_username</li>
       <li>branch</li>
       <li>commit_id</li>
       <li>commit_msg</li>
       <li>commit_url</li>
       <li>container_name</li>
       <li>event_id</li>
       <li>event_time</li>
       <li>event_type</li>
       <li>project_name</li>
       <li>repo_name</li>
       <li>repo_owner</li>
       <li>repo_provider</li>
       <li>repo_url</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td><code>BUILD</code></td>
    <td>Lists all build events associated with the specified project.</td>
    <td> 
      <ul>
       <li>author_name</li>
       <li>author_username</li>
       <li>branch</li>
       <li>build_end</li>
       <li>build_num</li>
       <li>build_start</li>
       <li>build_status</li>
       <li>commit_id</li>
       <li>commit_msg</li>
       <li>commit_url</li>
       <li>container_name</li>
       <li>event_id</li>
       <li>event_time</li>
       <li>event_type</li>
       <li>project_name</li>
       <li>repo_name</li>
       <li>repo_owner</li>
       <li>repo_provider</li>
       <li>repo_url</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td><code>DOCKERIMAGE</code></td>
    <td>Lists all Docker image events associated with the specified project.</td>
    <td> 
      <ul>
       <li>author_name</li>
       <li>author_username</li>
       <li>branch</li>
       <li>build_num</li>
       <li>commit_id</li>
       <li>commit_msg</li>
       <li>commit_url</li>
       <li>container_name</li>
       <li>container_registry_name</li>
       <li>container_registry_provider</li>
       <li>event_id</li>
       <li>event_time</li>
       <li>event_type</li>
       <li>image_sha</li>
       <li>project_name</li>
       <li>repo_name</li>
       <li>repo_owner</li>
       <li>repo_provider</li>
       <li>repo_ur</li>
      </ul>
    </td>
  </tr>
  <tr>
    <td><code>K8SDEPLOY</code></td>
    <td>Lists all Kubernetes deploy events associated with the specified project.</td>
    <td> 
      <ul>
       <li>auto_deploy</li>
       <li>cluster_name</li>
       <li>cluster_provider</li>
       <li>cluster_region</li>
       <li>deployment_description</li>
       <li>deployment_id</li>
       <li>deployment_name</li>
       <li>deployment_status</li>
       <li>event_id</li>
       <li>event_time</li>
       <li>event_type</li>
       <li>namespace</li>
       <li>project_name</li>
       <li>images:
         <ul>
           <li>author_username</li>
           <li>branch</li>
           <li>commit_id</li>
           <li>commit_msg</li>
           <li>docker_registry_name</li>
           <li>docker_registry_provider</li>
           <li>image</li>
           <li>image_sha</li>
           <li>repo_name</li>
           <li>repo_owner</li>
           <li>repo_provider</li>
         </ul>
       </li>
       <li>containers:
         <ul>
           <li>container_name</li>
           <li>image</li>
           <li>image_sha</li>
         </ul>
       </li>
      </ul>
    </td>
  </tr>
  <tr>
    <td><code>HELMCHARTDEPLOY</code></td>
    <td>Lists Helm chart deployments associated with the specified project.</td>
    <td> 
      <ul>
       <li>event_id</li>
       <li>event_time</li>
       <li>event_type</li>
       <li>project_name</li>
      </ul>
    </td>
  </tr>  
</table>
	
A typical response might look like this:

```
{
  "events": [
    {
      "author_username": "testuser",
      "branch": "master",
      "build_end": "2018-07-06T20:35:56.047Z",
      "build_num": 562141,
      "build_start": "2018-07-06T20:34:53.856Z",
      "build_status": "Success",
      "commit_id": "e8168fff49919952502b5148c2d62c0c0r8d42fd",
      "commit_msg": "Test",
      "commit_url": "https://github.com/testuser/mysql/commit/e8168fff49919952502b5148c2d62c0c0r8d42fd",
      "container_name": "mysql",
      "event_id": 100,
      "event_time": "2018-07-06T20:34:36.910Z",
      "event_type": "BUILD",
      "project_name": "guide",
      "repo_name": "mysql",
      "repo_owner": "testuser",
      "repo_provider": "github",
      "repo_url": "https://github.com/testuser/mysql"
    },
  "marker": "IplibDc3iFw=",
  "project_name": "Guide"
}
```

### Filtering

You can use the following filters with `events`:

* `build`
* `deploy`
* `dockerimage`
* `pullrequest`
* `push`
* `k8sdeploy`

```
curl "https://api.distelli.com/<YOUR_ACCOUNT_NAME>/projects/<YOUR_PROJECT_NAME>/events?apiToken=<YOUR_API_TOKEN>&filter_name=EventType&filter_event_type=<YOUR_FILTER>"
```
