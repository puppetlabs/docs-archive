---
layout: default
title: "Pipelines API"
---

## The Pipelines API

The Pipelines API is a RESTful application program interface (API). The API provides an interface to the Pipelines SaaS, providing users with the ability to interact with objects including:

* Deploying applications
* Listing and inspecting builds
* Adding servers to environments
* Setting environment variables

### Getting started

Every call to the API requires two parameters:

* [Your Pipelines username](./account.html)
* [Your Pipelines API token](./api-token.html)

Here is an example API call that will list the applications in account `jdoe`.

~~~
curl "https://api.distelli.com/jdoe/apps?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

### Using curl

Curl is a command-line tool for transferring data using various protocols. It is available [here](http://curl.haxx.se/). Curl is installed by default on many operating systems.

The curl options that may be required for use with the Pipelines API include:

<table>
	<tr>
		<td><code>-d</code></td>
		<td>Specifies that the API request will include data in the body. When using <code>-d</code> you must also use the <code>-H</code> option.</td>
	</tr>
	<tr>
		<td><code>-H "Content-Type: application/json"</code></td>
		<td>When specifying JSON data in the body of the API request, this option is required for curl to interpret the data format.</td>
	</tr>
	<tr>
		<td><code>-i</code></td>
		<td>Include the HTTP header results. This may be handy to see the response code if there is no data returned.</td>
	</tr>
	<tr>
		<td><code>-X *VERB*</code></td>
		<td>Specify what type of query. The default is GET. Pipelines API verbs include:
			<ul>
				<li>DELETE</li>
				<li>GET</li>
				<li>PATCH</li>
				<li>POST</li>
				<li>PUT</li>
			</ul>
		</td>
	</tr>
</table>

**Typical syntax**

~~~
curl OPTIONS "URL" -d JSON_BODY
~~~

## Making Pipelines API calls with curl

Different types of calls (such as GET, DELETE, or POST) require differing options. Except GET, these query verbs require the `-X` option. Here are some GET examples.

In this example `GetEnv` API call, user `jdoe` is getting information about environment `SA_Linux`.

~~~
curl -i "https://api.distelli.com/jdoe/envs/SA_Linux?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

In this example `ListAppBuilds` API call, user `jdoe` is listing builds for application `gobuilddeploy`.

~~~
curl -i "https://www.distelli.com/jdoe/apps/gobuilddeploy/builds?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

In this example `ListEnvDeployments` API call, user `jdoe` is listing deployments for environment `SA_Linux`.

Also of note in this example is the use of pagination. The `max_results` option tells the API to only return two results, and the `order=desc` option tells the API to sort the results in descending order. For more information on pagination, see [Pipelines API pagination](./api.html#pagination).

~~~
curl -i "https://api.distelli.com/jdoe/envs/SA_Linux/deployments?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z8&max_results=2&order=desc"
~~~

## Including data in the API call

The Pipelines API accepts and sends JSON-formatted data. To send the data with curl you must include the `-d` option. The data must be JSON formatted.

For more information on the JSON format, see [json.org](http://json.org/).

In the below example, user `jdoe` is creating a new application called `NewApp1` and adding a description: `"This is a description"`.

~~~
curl -i -X PUT -H "Content-Type: application/json" "https://api.distelli.com/jdoe/apps/NewApp1?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z" -d '{"description": "This is a description"}'
~~~

## Language-specific Pipelines API client examples

This section provides language-specific references for client access to the Pipelines API.

### Node.js

The below snippet of code requires that you:

1. npm install node-rest-client
2. export DISTELLI_USERNAME=**YOUR_DISTELLI_USERNAME**
3. export DISTELLI_APITOKEN=**YOUR_DISTELLI_TOKEN**

This code lists applications and creates application `NewApp001`.

~~~
var Client = require('node-rest-client').Client;

var apiToken = process.env.DISTELLI_APITOKEN;
var username = process.env.DISTELLI_USERNAME;
var apiurl = "https://api.distelli.com";

client = new Client();

// Example distelli ListApps GET
client.get(apiurl + '/' + username + '/apps?apiToken=' + apiToken, function(data, response){
            // parsed response body as js object
            console.log(data);
            // raw response
            //console.log(response);
        });

// Example distelli CreateApp PUT with JSON body
var newappname = "NewApp001"
var jsonbody = {
  description: "This is the new application description"
};

client.put(apiurl + '/' + username + '/apps/' + newappname + '?apiToken=' + apiToken, jsonbody, function(data,response) {
    // parsed response body as js object
    console.log(data);
    // raw response
    //console.log(response);
});
~~~

You can clone the example above at the [Distelli/api-example](https://github.com/Distelli/api-example) GitHub repository. 

### Python

The below snippet of code requires that you:

1. export DISTELLI_USERNAME=**YOUR_DISTELLI_USERNAME**
2. export DISTELLI_APITOKEN=**YOUR_DISTELLI_TOKEN**

This code lists applications and creates application `NewApp002`.

~~~
import requests
import json
import os

apiToken = os.environ['DISTELLI_APITOKEN']
username = os.environ['DISTELLI_USERNAME']
apiurl = "https://api.distelli.com"

# Example distelli ListApps call
listappsurl=apiurl + '/' + username + '/apps?apiToken=' + apiToken
response = requests.get(listappsurl)

#Careful, the will print your token
#print(response.url)
print(response.text)

# Example distelli CreateApp call
newappname = "NewApp002"
jsonbody = json.dumps({'description': 'This is the new application description'})
createappurl=apiurl + '/' + username + '/apps/' + newappname + '?apiToken=' + apiToken
response = requests.put(createappurl, data=jsonbody)

#Careful, this will print your token
#print(response.url)
print(response.text)
~~~

You can clone the example above at the [Distelli/api-example](https://github.com/Distelli/api-example) GitHub repository. 

### Ruby

The below snippet of code requires that you:

1. gem install mime-types
2. gem install netrc
3. gem install http-cookies
4. gem install json
5. gem install rest-client
6. export DISTELLI_USERNAME=**YOUR_DISTELLI_USERNAME**
7. export DISTELLI_APITOKEN=**YOUR_DISTELLI_TOKEN**

This code lists applications and creates application `NewApp003`.

~~~
require 'rest-client'
require 'json'

apiToken = ENV["DISTELLI_APITOKEN"]
username = ENV["DISTELLI_USERNAME"]
apiurl = "https://api.distelli.com"

# Example distelli ListApps call
listappsurl=apiurl + "/" + username + '/apps?apiToken=' + apiToken
response = RestClient.get listappsurl, {:accept => :json}
print response.code
print response.headers
print response.body

# Example distelli CreateApp call
newappname = "NewApp003"
jsonbody = {:description => "This is the new application description"}.to_json
createappurl=apiurl + '/' + username + '/apps/' + newappname + '?apiToken=' + apiToken
response = RestClient.put createappurl, jsonbody, :content_type => :json, :accept => :json
print response.code
print response.headers
print response.body
~~~ 

If you see the following error, you have not set the environment variables (steps 6 & 7 above).

~~~
distelli_api_ruby_example.rb:9:in `+': can't convert nil into String (TypeError)
~~~

Also, if application `NewApp003` already exists, you will receive an error.

You can clone the example above at the [Distelli/api-example](https://github.com/Distelli/api-example) GitHub repository.

### Windows PowerShell 

The below snippet of code requires that you: 

1. Install PowerShell version 3.0 or newer
1. Set environment to DISTELLI_USERNAME=**YOUR_DISTELLI_USERNAME**
1. Set environment to DISTELLI_APITOKEN=**YOUR_DISTELLI_TOKEN**

This code lists applications and creates application `NewApp004`. 

~~~
$apiToken = $env:ApiToken 
$username = $env:Username
$apiUrl   = $env:ApiUrl

$appsUri = "https://$apiUrl/$Username/apps?apiToken=$ApiToken"
$appsCollection = Invoke-RestMethod -Uri $appsUri

Write-Output "Existing Apps"

Write-Output $appsCollection.apps.name

$jsonBody = @{Description = 'An Awesome New App'} | ConvertTo-JSON

$newAppName = 'NewApp004'


# $NewAppName var is in brackets because we don't want the ? character
# interpreted as a part of the variable name. It breaks the URI.
$params = @{
  Uri = "https://$apiUrl/$username/apps/${newAppName}?apiToken=$apiToken"
  Method = 'Put'
  ContentType = 'application/json'
  Body = $jsonBody
}

$returnValue = Invoke-RestMethod @params

Write-Output "App We Created"

Write-Output $returnValue.app.name
~~~

You can clone the example above at the [Distelli/api-example](https://github.com/Distelli/api-example) GitHub repository. 

## API Guide

This describes the resources that make up the official Pipelines API. 

### Schema

All API access is over HTTPS, and accessed from the `api.distelli.com` domain. All data is sent and received as JSON. For more information on the JSON format, see [json.org](http://json.org/).

~~~
$ curl -i "https://api.distelli.com/jdoe/apps?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"

HTTP/1.1 200 OK
Content-Type: application/json
Server: DistelliWS
x-dstli-rid: etow4x4rngn007nsn65yq8b08
Content-Length: 2290
Connection: keep-alive
~~~

Blank fields are included as `null` instead of being omitted.

All timestamps are returned in ISO 8601 format:

~~~
YYYY-MM-DDTHH:MM:SSZ
~~~

### HTTP Verbs

<table>
    <tbody>
        <tr>
            <td><b>Verb</b></td><td><b>Description</b></td>
        </tr>
        <tr>
            <td>DELETE</td><td>Used for deleting resources.</td>
        </tr>
        <tr>
            <td>GET</td><td>Used for retrieving resources.</td>
        </tr>
        <tr>
            <td>PATCH</td><td>Used for updating resources with partial JSON data.</td>
        </tr>
        <tr>
            <td>POST</td><td>Used for creating resources.</td>
        </tr>
        <tr>
            <td>PUT</td><td>Used for replacing resources or collections.</td>
        </tr>
    </tbody>
</table>

### Authentication

You must use a Pipelines-supplied API token to authenticate API calls. See [Pipelines API tokens](./api-token.html) for instructions on generating and retrieving your API token. 

<h3><a name="hypermedia"></a>Hypermedia</h3>

Resources may have one or more __*_url__ properties linking to other resources. These are meant to provide explicit URLs so that proper API clients don’t need to construct URLs on their own. It is highly recommended that API clients use these. Doing so will make future upgrades of the API easier for developers.

<h3><a name="url-encoding"></a>URL encoding</h3>

Fields that are included as URL parameters require URL encoding. For example, when searching for a server tag that contains a space `GOLD IMAGE`:

~~~
curl -i "https://api.distelli.com/jdoe/servers/tags/GOLD+IMAGE?apiToken=abcdefghijklmnopqrstuvwxyz0123456789z"
~~~

<h3><a name="pagination"></a>Pagination</h3>

List requests that return multiple items are paginated to 10 items by default. You can specify further pages with the `marker` query string parameter. For some resources, you can also set a custom page size up to 100 with the `max_results` parameter.

The following example, lists the applications and sets `max_results` to three.

~~~
curl -i "https://api.distelli.com/USERNAME/apps?apiToken=TOKEN&max_results=3"
~~~

Below is an example response to the above call.

~~~
{
  "apps": [
    {
      "api_url": "https://api.distelli.com/jdoe/apps/AdvancedGoApp",
      "created": "2015-04-24T00:13:08Z",
      "description": null,
      "html_url": "https://www.distelli.com/jdoe/apps/AdvancedGoApp",
      "name": "AdvancedGoApp",
      "owner": "jdoe"
    },
    {
      "api_url": "https://api.distelli.com/jdoe/apps/AdvancedNodeApp",
      "created": "2015-04-24T16:50:40Z",
      "description": "Advanced Node App 12:25",
      "html_url": "https://www.distelli.com/jdoe/apps/AdvancedNodeApp",
      "name": "AdvancedNodeApp",
      "owner": "jdoe"
    },
    {
      "api_url": "https://api.distelli.com/jdoe/apps/ApachePHPApp",
      "created": "2015-05-08T00:00:36Z",
      "description": null,
      "html_url": "https://www.distelli.com/jdoe/apps/ApachePHPApp",
      "name": "ApachePHPApp",
      "owner": "jdoe"
    }
  ],
  "marker": "HsPj41f5DiOwHxnm88gZog=="
}
~~~

To list the subsequent three applications, use this call:

~~~
curl -i "https://api.distelli.com/USERNAME/apps?apiToken=TOKEN&max_results=3&marker=HsPj41f5DiOwHxnm88gZog=="
~~~

### Sorting

List requests that return multiple items can be sorted alphabetically **asc**ending (use `asc`) or **desc**ending (use `desc`) with the `order` query string parameter.

For example, to list applications in descending order:

~~~
curl -i "https://api.distelli.com/USERNAME/apps?apiToken=TOKEN&order=desc"
~~~

### Responses

On success, the API will return a <code>200 OK</code>, possibly with a JSON response. If the API call fails it will return a <code>4xx</code> error code with JSON indicating the error or a <code>500</code> if there is an internal server error. 

