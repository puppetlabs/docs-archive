---
layout: default
title: "Superuser API calls"
---

Pipelines API calls can be made for any user by a Pipelines superuser. This is done by using the superuser Pipelines API token.


<h3>Users</h3>

Currently there is no over-arching API call to get a list of Users in a Pipelines onpremise installation. The following queries will return a list of users in a Pipelines installation.

<h5>DynamoDB</h5>

<code>aws dynamodb scan --table-name logins-REGION-STAGE --projection-expression 'NName' | jq -r '.Items[] | select(has("NName")) | "\(.NName.S)"' | tee /tmp/users</code>

<ul>
  <li><b>REGION</b> - The REGION that was specified when installing the Pipelines stack specified in <code>distelli-install.sh</code></li>
  <li><b>STAGE</b> - The STAGE that was specified when installing the Pipelines stack. This is either:</li>
  <ul>
    <li>beta</li>
    <li>gamma</li>
    <li>prod</li>
  </ul>
</ul>

An example may look like this where REGION=us-east-1 and STAGE=prod.

<code>aws dynamodb scan --table-name logins-us-east-1-prod --projection-expression 'NName' | jq -r '.Items[] | select(has("NName")) | "\(.NName.S)"' | tee /tmp/users</code>

The above requires an authenticated AWS CLI for executing the "aws" command and jq 1.5+ to parse the json reply.


<h5>MySQL</h5>

<code>select `#`->>"$.NName" as Username from logins</code>

An example response:

~~~
+----------+
| Username |
+----------+
| distelli |
| bmcgehee |
| mindy    |
+----------+
~~~

<h3>Make API Calls</h3>

A super user of Pipelines can use their API token to access any users account in Pipelines. This functionality can provide metrics for reporting.

For more information on making API calls to Pipelines, please see [Getting Started with Pipelines API](./api.html).


<h3>Example</h3>

Here is an example script that will count the number of builds in a selection of user accounts.


~~~
#!/bin/bash
# 
# This script requires you set $APITOKEN to the superuser API Token found in Distelli.
#
# This script requires that you query the Distelli DB for a list of users.
#
#
# *DynamoDB*
#
# aws dynamodb scan –table-name logins-REGION-STAGE –projection-expression ‘NName’ | jq -r ‘.Items[] | select(has(“NName”)) | “(.NName.S)“’ | tee /tmp/users
#
#
# *MySQL*
#
# select #->>“$.NName” as Username from logins
#
users="USER1 bmcgehee jdoe exampledotcom ZZZ2"
numresults=100
for user in $users; do
  echo "User: $user"
  count=0
  marker="null"
  while true; do
    if [ "$marker" == "null" ]; then
      results=$(curl -s "https://api.distelli.com/$user/builds?apiToken=$APITOKEN&max_results=$numresults&order=desc")
      marker=$(echo "$results" | jq .marker -r)
    else
      results=$(curl -s "https://api.distelli.com/$user/builds?apiToken=$APITOKEN&max_results=$numresults&order=desc&marker=$marker")
      marker=$(echo "$results" | jq .marker -r)
    fi
    resultcount=$(echo "$results" | jq '.builds | length')
    count=$((count+resultcount))
    echo "Counting: $count"
    if [ "$marker" == "null" ]; then
      break
    fi
  done
  echo "COUNT: $count"
done
~~~


