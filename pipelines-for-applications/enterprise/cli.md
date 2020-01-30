---
layout: default
title: "The Pipelines CLI"
---
<br>
The Pipelines CLI provides command line functionality for interfacing with the Pipelines SaaS. 

The Pipelines CLI and Pipelines agent are unified into one package.

The Pipelines CLI is used to create, build, push, and deploy applications. With the Pipelines CLI you can create your application in Pipelines without the need for a repository.

## Installing the CLI

<h4><a name="linux-and-mac-os-x"></a>Linux and macOS X</h4>

To install on Linux/Mac you can use either curl <b>or</b> wget with one of the following syntaxes.

<h5>wget example</h5>

~~~
wget -qO- https://pipelines.puppet.com/download/client | sh
~~~

<h5>curl example</h5>

~~~
curl -sSL https://pipelines.puppet.com/download/client | sh
~~~

<h4><a name="windows"></a>Windows</h4>

To install on Windows, copy and paste the following PowerShell command into a command (cmd) window.

~~~
powershell -NoProfile -ExecutionPolicy Bypass -Command "iex ((new-object net.webclient).DownloadString('https://pipelines.puppet.com/download/client.ps1'))" & SET PATH=%PATH%;%ProgramFiles%/Distelli
~~~

<h3><a name="login-and-validate"></a>Login and Validate</h3>

To complete, and validate, the CLI installation; issue a `distelli login` command to login to your Puppet Pipelines account.

> **Important:** This requires that you have already completed the [Creating an Account](./account.html) steps.

~~~
# distelli login

Email: jdoe@distelli.com
Password:
~~~

> **Important:** If you enter your account password incorrectly too many times, a `Failed to login to your Distelli Account, Please check your credentials`  message will appear, and your account will be locked as a security precaution. The lock expires in two hours. 

## Upgrading the CLI

To upgrade the Pipelines CLI, re-install the CLI on top of the existing CLI. This will perform an upgrade.

## CLI events

The Pipelines CLI can be used to create application events in Pipelines applications. Typically this would be used in conjunction with a third-party build system; Jenkins, for example.

The events that can be created include:

<ul>
  <li><b>Push</b> - A software repository push (commit) event.</li>
  <li><b>PullRequest</b> - A software repository pull request event.</li>
  <li><b>Build</b> - A third-party build event.</li>
  <li><b>DockerImage</b> - A Docker image event.</li>
</ul>

An example usage of the Pipelines CLI to create an event:

~~~
distelli event -app bmcgehee/cli-demo -type build -build Jenkins:1234 -build-url http://jenkins.example.com/builds/1234 -build-status Queued
~~~

<table>
  <tr><th colspan="3">Event Option</th></tr>
  <tr><th>EventType</th><th>Required</th><th>Explanation</th></tr>

  <tr><td colspan="3"><b>-profile-dir &lt;DIR&gt;</b></td></tr>
  <tr><td>All</td><td>No</td><td>Location of profile information (like cached credentials), defaults to ~/, may also be set via DISTELLI_PROFILE environment variable.</td></tr>

  <tr><td colspan="3"><b>-app &lt;See Explanation&gt;</b></td></tr>
  <tr><td>All</td><td>Yes</td><td>For Pipelines for Applications, the format is: USERNAME/APP-NAME<br>
For Pipelines for Containers, the format is: USERNAME/PROJECT-NAME.CONTAINER-NAME</td></tr>

  <tr><td colspan="3"><b>-type &lt;EVENT-TYPE&gt;</b></td></tr>
  <tr><td>All</td><td>Yes</td><td>Event types include the following:<br><ul><li>Push</li><li>PullRequest</li><li>Build</li><li>DockerImage</li></ul></td></tr>

  <tr><td colspan="3"><b>-build &lt;BUILD-PROVIDER&gt;:&lt;BUILD-ID&gt;</b></td></tr>
  <tr><td>Build</td><td>No</td><td>The build provider and the unique build id. Valid options for build provider include:<br><ul><li>CircleCLI</li><li>CodeShip</li><li>Jenkins</li><li>TravisCI</li><li>AzureDevops</li></ul></td></tr>

  <tr><td colspan="3"><b>-build-url &lt;URL&gt;</b></td></tr>
  <tr><td>Build</td><td>No</td><td>The URL to the build.</td></tr>

  <tr><td colspan="3"><b>-build-status &lt;BUILD-STATUS&gt;</b></td></tr>
  <tr><td>Build</td><td>No</td><td>The current status of the build. This can be updated when using the same BUILD-ID. Valid Status' include:<br><ul><li>Queued</li><li>Waiting</li><li>Running</li><li>*TimedOut</li><li>*Failed</li><li>*Success</li></ul>* These are terminal conditions and cannot be changed after they are set.</td></tr>

  <tr><td colspan="3"><b>-build-start &lt;ISO-8601-DATE-TIME&gt;</b></td></tr>
  <tr><td>Build</td><td>No</td><td>When the build started. If a new build event is created, this value does not need to be specified and will default to <b>now</b>. Format example: 2016-12-09T14:25:33Z</td></tr>

  <tr><td colspan="3"><b>-build-end &lt;ISO-8601-DATE-TIME&gt;</b></td></tr>
  <tr><td>Build</td><td>No</td><td>When the build ended. If a build event is updated to a terminal build status, this value does not need to be specified and will default to <b>now</b>. Format example: 2016-12-09T14:25:33Z</td></tr>

  <tr><td colspan="3"><b>-autobuild | -noautobuild</b></td></tr>
  <tr><td>Build</td><td>No</td><td>Flag the build event as autobuild or noautobuild.</td></tr>

  <tr><td colspan="3"><b>-image &lt;REGISTRY-PROVIDER&gt;:&lt;DOCKER-REPOSITORY&gt;:&lt;TAG&gt;</b></td></tr>
  <tr><td>Image</td><td>No</td><td>The image that was pushed (and maybe built).<br> REGISTRY-PROVIDER includes:<ul><li>DOCKERHUB</li><li>ECR</li><li>PRIVATE</li><li>GCR</li></ul>The TAG is optional.<br>When using this option a local <code>docker inspect &lt;REGISTRY-PROVIDER&gt;:&lt;DOCKER-REPOSITORY&gt;:&lt;TAG&gt;</code> is ran to obtain metadata about this docker image.<br><br>This option may be specified multiple times.</td></tr>

  <tr><td colspan="3"><b>-image-registry-id &lt;ECR-REGISTRY-ID&gt;</b></td></tr>
  <tr><td>Image</td><td>No</td><td>The aws account id of the ECR registry. Typically an ECR Docker repository that you pull looks like this: https://aws_account_id.dkr.ecr.region.amazonaws.com This is the aws_account_id portion. Omit this option if not using ECR. If specified multiple times, the first ECR-REGISTRY-ID will be paired with the first --image option in order.</td></tr>

  <tr><td colspan="3"><b>-image-registry-endpoint &lt;REGISTRY-SERVER&gt;</b></td></tr>
  <tr><td>Image</td><td>No</td><td>This is the server endpoint which you would pass to `docker login`. If specified multiple times, the first REGISTRY-SERVER will be paired with the first --image option in order. </td></tr>

  <tr><td colspan="3"><b>-image-registry-name &lt;REGISTRY-NAME&gt;</b></td></tr>
  <tr><td>Image</td><td>No</td><td>This is the name for the docker repository displayed in the Pipelines interface. If specified multiple times, the first REGISTRY-NAME is paired with the first --image option in order.</td></tr>  

  <tr><td colspan="3"><b>-commit &lt;SCM-PROVIDER&gt;:&lt;COMMIT-ID&gt;</b></td></tr>
  <tr><td>All</td><td>No</td><td>SCM-PROVIDER is one of:<br><ul><li>GIT</li><li>HG</li></ul>COMMIT-ID is the unique identifier of the commit that was pushed.</td></tr>  

  <tr><td colspan="3"><b>-commit-msg &lt;TEXT&gt;</b></td></tr>
  <tr><td>All</td><td>No</td><td>The content of the commit message. Defaults to inspecting the specified COMMIT-ID of the SCM-PROVIDER.</td></tr> 

  <tr><td colspan="3"><b>-commit-url &lt;URL&gt;</b></td></tr>
  <tr><td>All</td><td>No</td><td>The URL which provides information about this commit.</td></tr> 

  <tr><td colspan="3"><b>-repository &lt;REPO-PROVIDER&gt;:&lt;REPO-OWNER&gt;/&lt;REPO-NAME&gt;</b></td></tr>
  <tr><td>All</td><td>No</td><td>The source repository provider. REPO-PROVIDER is one of:<br><ul><li>BITBUCKET</li><li>BITBUCKETSERVER</li><li>GITHUB</li><li>GITHUBENTERPRISE</li><li>GITLAB</li><li>AZUREDEVOPS</li></ul></td></tr> 

  <tr><td colspan="3"><b>-repository-branch &lt;BRANCH-NAME&gt;</b></td></tr>
  <tr><td>All</td><td>No</td><td>The name of the repository branch that was pushed to.</td></tr> 

  <tr><td colspan="3"><b>-repository-author &lt;REPO-USERNAME&gt;/&lt;DISPLAY-NAME&gt;</b></td></tr>
  <tr><td>All</td><td>No</td><td>The username for the REPO-PROVIDER that pushed the commit.</td></tr> 

</table>

<h3>Workflow</h3>

When integrating a third-party build system with Pipelines, using the Pipelines CLI, the event flow is typically the following. 

1. `distelli event -app USERNAME/APP-NAME -type push`
1. `distelli event -app USERNAME/APP-NAME -type build -build PROVIDER:UID -build-status [QUEUED | WAITING]`
1. `distelli event -app USERNAME/APP-NAME -type build -build PROVIDER:UID -build-status RUNNING`
1. `distelli event -app USERNAME/APP-NAME -type build -build PROVIDER:UID -build-status [TIMEDOUT | FAILED | SUCCESS]`
1. `distelli event -app USERNAME/APP-NAME -type image REGISTRY-PROVIDER:DOCKER-REPOSITORY:TAG`

The above flow represents:
<ul>
  <li>The initial commit (push).</li>
  <li>Starting a build in status Queued or Waiting.</li>
  <li>Updating the build status to Running.</li>
  <li>Updating the build termination status to either Timedout, Failed, or Success.</li>
  <li>The final "image" event would only be necessary if there was a Docker image built during the build process.</li>
</ul>


<h3>Push & PR Events</h3>

A Push or PR event in Pipelines represents a software code event. Typically in Continuous Integration (CI), when a developer checks in code (push) it triggers a build system to initiate a build. In the scenario where a Puppet Pipelines user is using a third-party build system, the Pipelines CLI events can accommodate the creation of a Push or PullRequest event in the Pipelines application history. This event, in Pipelines, can provide links to the repository and commit.

If the Pipelines CLI event type "Push" (or PullRequest) is executed from the root of a directory; where the software being built was cloned to, and there is a .git or .hg directory, the Pipelines CLI can gather all the information necessary related to the last commit (push). One can run:

~~~
distelli event -app USERNAME/APP-NAME -type push | pullrequest 
~~~

If the .git | .hg directories are not available, the various push | pullrequest information can be manually supplied, like so:

~~~
distelli event -app bmcgehee/example-node-docker -type push | pullrequest -commit GIT:abcd -commit-msg "testing this" -commit-url https://github.com/doct15/example-node-docker/commit/a442e9b5c5b014d2bda745b0ba58e9999ac89c18 -repository GITHUB:doct15/example-node-docker -repository-branch master -repository-author "doct15/Brian McGehee"
~~~

> **Caution:** If the directory where the <code>distelli event -type push | pullrequest</code> occurs has a .git or .hg directory, Pipelines will attempt to get the commit id SHA and the commit message. These values will override any specified as command line options.

<h3>Build Events</h3>

A build event, in Pipelines, will provide a means for users to navigate to the build. This event will also live track the status of the build. And provide information on when the build started, stopped, and duration.

Build events are tracked, specifically, by the BUILD-ID specified in the <b>-build</b> option. This means future updates to a build, must address that build event by the BUILD-ID. Below is an example showing the creating and updates of BUILD-ID xyz3457:

~~~
distelli event -app USERNAME/APP-NAME -type build -build jenkins:xyz3457 -build-url http://jenkins.example.com/xyz3456 -build-status Queued -autobuild
distelli event -app USERNAME/APP-NAME -type build -build jenkins:xyz3457 -build-status Running
distelli event -app USERNAME/APP-NAME -type build -build jenkins:xyz3457 -build-status Success
~~~

The above will create a build event, in Pipelines, identified by BUILD-ID xyz3457 in a build status of Queued. The following two lines will update the same build event status from Queued to Running, and finally from Running to Success. After setting build xyz3457 to a termination state of "Success", this build can not be updated.

<img src="images/newcli-build-status.png" alt="Build status">


<h3>Image Events</h3>

An <b>Image</b> event in Pipelines represents an image that was built. If using a third-party build system to build Docker images, these images are tracked in Pipelines.

To create an image event, use the following syntax:

~~~
distelli event -app USERNAME/APP-NAME -type dockerimage -image REGISTRY-PROVIDER:DOCKER-REPOSITORY:TAG
~~~

The Pipelines CLI will use the <code>docker inspect</code> command to find information about the last built image that matches REGISTRY-PROVIDER:DOCKER-REPOSITORY:TAG.

You can create many events for a single Docker image inspection details, but the image inspection details will not change unless the image itself actually changes.

Further information can be specified when pushing images as exemplified below:

~~~
distelli event -app bmcgehee/cli-demo -type Dockerimage -image dockerhub:doct15/cli-demo:1234 -image-registry-name "doct15/cli-demo" -repository-branch branch3 -repository-author doct15/Brian -commit-msg "Testing"
~~~




