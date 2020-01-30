---
layout: default
title: "Example group permission roles"
---

Here are permission settings for four example user roles.  

Give users permissions by adding them to a user group. For more information, see [Groups](./group.html) and [Users](./users.html). 

Here are the four example roles:
* **Read-only**: A user who has read-only access to team activities, including; builds, releases, and deploys.
* **Deploy and build any application**: A user who can create, build, and deploy any team application.
* **Deploy and build a single application**: A user who can build and deploy a specific application.
* **Manager**: A user who manages the team account.

<table>
  <tr>
    <th>Group</th>
    <th>Application</th>
    <th>Environment</th>
    <th>Agent</th>
    <th>Settings</th>
    <th>Billing</th>
    <th>Credentials</th>
    <th>Teams</th>
    <th>Groups</th>
  </tr>
  <tr>
   <td><b>Read Only</b></td>
   <td>Read Only</td>
   <td>Read Only</td>
   <td>Full Control</td>
   <td>Read Only</td>
   <td>Read Only</td>
   <td>Read Only</td>
   <td>Read Only</td>
   <td>Read Only</td>
  </tr>
  <tr>
   <td><b>Deploy and build any application</b></td>
   <td>Deploy Only</td>
   <td>Deploy Only</td>
   <td>Full Control</td>
   <td>Read Only</td>
   <td>No Access</td>
   <td>No Access</td>
   <td>No Access</td>
   <td>No Access</td>
  </tr>
  <tr>
   <td><b>Deploy and build a single application</b></td>
   <td>No Access,<br/>1 overwrite</td>
   <td>Deploy Only</td>
   <td>Full Control</td>
   <td>No Access</td>
   <td>No Access</td>
   <td>No Access</td>
   <td>No Access</td>
   <td>No Access</td>
  </tr>
  <tr>
   <td><b>Manager</b></td>
   <td>Read Only</td>
   <td>Read Only</td>
   <td>No Access</td>
   <td>Read Only</td>
   <td>Read Only</td>
   <td>Read Only</td>
   <td>Read Only</td>
   <td>Read Only</td>
  </tr>
</table>

## Read only

Use the following settings:

<table>
 <tr>
     <th>Group</th>
     <th>Application</th>
     <th>Environment</th>
     <th>Agent</th>
     <th>Settings</th>
     <th>Billing</th>
     <th>Credentials</th>
     <th>Teams</th>
     <th>Groups</th>
 </tr>
 <tr>
     <td><b>Read Only</b></td>
     <td>Read Only</td>
     <td>Read Only</td>
     <td>Full Control</td>
     <td>Read Only</td>
     <td>Read Only</td>
     <td>Read Only</td>
     <td>Read Only</td>
     <td>Read Only</td>
 </tr>
</table>

These permissions allow a user to view most details for the team. They are not able to deploy or make changes.

A user assigned these permissions can view all:
* Applications
* Application environments
* Servers
* Builds
* Releases
* Deployments

They cannot view: 
* Application settings
* Environment settings
* Team global settings


## Deploy and build any application

Use the following settings:

<table>
 <tr>
     <th>Group</th>
     <th>Application</th>
     <th>Environment</th>
     <th>Agent</th>
     <th>Settings</th>
     <th>Billing</th>
     <th>Credentials</th>
     <th>Teams</th>
     <th>Groups</th>
 </tr>
 <tr>
     <td><b>Deploy Applications</b></td>
     <td>Deploy Only</td>
     <td>Deploy Only</td>
     <td>Full Control</td>
     <td>Read Only</td>
     <td>No Access</td>
     <td>No Access</td>
     <td>No Access</td>
     <td>No Access</td>
 </tr>
</table>

These permissions allow a user to build and deploy all applications in the team.

A user assigned these permissions can:
* Build and deploy any application
* Deploy to any application environments
* Manage servers
* View all builds
* View all releases
* View all deployments

They cannot:
* View application settings
* View environment settings
* View team global settings

## Deploy and build a single application

Use the following settings:

<table>
 <tr>
     <th>Group</th>
     <th>Application</th>
     <th>Environment</th>
     <th>Agent</th>
     <th>Settings</th>
     <th>Billing</th>
     <th>Credentials</th>
     <th>Teams</th>
     <th>Groups</th>
 </tr>
 <tr>
     <td><b>Deploy Single App</b></td>
     <td>No Access, <br/>1 overwrite</td>
     <td>Deploy Only</td>
     <td>Full Control</td>
     <td>No Access</td>
     <td>No Access</td>
     <td>No Access</td>
     <td>No Access</td>
     <td>No Access</td>
 </tr>
</table>

These permissions allow a user to build and deploy on a specific application. In this example, **Applications** is set to **No Access** and a **Full Access** override is granted to one application. Note that you can grant full access to more than one application.

A user assigned these permissions can:
* Build and deploy a specific application
* Deploy to the specific application environments
* Manage servers
* View all releases
* View all deployments

They cannot:
* View application settings
* View environment settings
* View team global settings

## Manager

Use the following settings:
<table>
 <tr>
     <th>Group</th>
     <th>Application</th>
     <th>Environment</th>
     <th>Agent</th>
     <th>Settings</th>
     <th>Billing</th>
     <th>Credentials</th>
     <th>Teams</th>
     <th>Groups</th>
 </tr>
 <tr>
     <td><b>Manager</b></td>
     <td>Read Only</td>
     <td>Read only</td>
     <td>No Access</td>
     <td>Full Control</td>
     <td>Full Control</td>
     <td>No Access</td>
     <td>Full Control</td>
     <td>Full Control</td>
 </tr>
</table>

A user assigned these permissions can:
* View all applications
* View all application environments
* View all servers
* View all builds
* View all releases
* View all deployments
* View team global settings
* Edit the billing plan
* Edit groups
* Edit teams
* Edit integrations

They cannot:
* View application settings
* View environment settings
* View credentials




