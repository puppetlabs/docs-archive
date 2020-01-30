---
layout: default
title: "Integrate Bitbucket"
---

Bitbucket is a software repository hosting service that provides revision control.

Integrating Bitbucket with Pipelines provides continuous integration and continuous deployment. 

<h2><a name="integrate-with-bitbucket"></a>Integrate with Bitbucket</h2>

With Bitbucket integration you can create applications in Pipelines from a Bitbucket repository and enable auto-builds when a change is checked into the repository.

Before beginning, ensure you are using the correct user account in Puppet Pipelines.

## Integrate with Bitbucket 

1. Click **Integrations**.
1. Click **Connect Bitbucket** to open Bitbucket's authorization page in a new browser tab.
1. Click **Grant access**.
1. In Pipelines, click **Integrations**. 
1. Copy the SSH key.
1. On the Bitbucket website, go to **Bitbucket settings** and add the SSH key to your Bitbucket account. For more information, see [Adding a public key to your Bitbucket Settings](https://confluence.atlassian.com/bitbucket/set-up-an-ssh-key-728138079.html#SetupanSSHkey-Step4.AddthepublickeytoyourBitbucketsettings)

## Revoke Pipelines' access to Bitbucket

For information on revoking Pipelines' access to Bitbucket, see [Revoking OAuth Access](https://confluence.atlassian.com/jirasoftwareserver0710/allowing-oauth-access-953147226.html)

## Integrate with Bitbucket Server

With Bitbucket Server integration you can create applications in Pipelines from a Bitbucket Server repository and enable auto-builds when a change is checked into the repository.

<h3><a name="integrate-with-bitbucket-server"></a>Integrate with Bitbucket Server</h3>

Before beginning, ensure you are using the correct user account in Puppet Pipelines.

To enable Bitbucket Server integration, follow these steps.

<ol>
<li>In the Pipelines web UI click the <b>gear</b> icon at the top right.</li>
<li>Click the <b>Integrations</b> link on the left.</li>
<li>Click the <b>Bitbucket Server</b> button.</li>
<li>Enter your Bitbucket Server <b>Host</b>.</li>
<li>Enter the Bitbucket Server <b>Username</b>.</li>
<li>Enter the Bitbucket Server <b>Password</b>.</li>
<li>Enter the <b>SSH Port</b>.</li>

<p>The default SSH Port is <b>22</b>.</p>

<li>Click the <b>Add Credentials</b> button.</li>
</ol>

## Bitbucket OAuth Permissions

When building applications that interact with Bitbucket users it is helpful to understand the permissions.

To make API calls that interact with Bitbucket users you must first create an OAuth consumer. This OAuth consumer must specify the permissions necessry for the application. To setup your OAuth consumer token in Bitbucket:

1. Login to <a href="http://bitbucket.org" target="_blank">Bitbucket</a>
2. On the top right click on your profile drop down and select <b>Manage account</b>.
3. In the left menu select <b>OAuth</b>.
4. Click the <b>Add consumer</b> button.

Here you can configure your OAuth consumer details and permissions. Below outlines the permissions and what message the user sees when the authorize your access.

<table>
<tr><td><b>Type</b></td><td><b>Access</b></td><td><b>Request to User</b></td></tr>
<tr><td>Account</td><td>Email</td><td>Read your account's primary email address</td></tr>
<tr><td></td><td>Read</td><td>Read your account information</td></tr>
<tr><td></td><td>Write</td><td>Read and write to your account information</td></tr>
<tr><td></td><td></td><td></td></tr>
<tr><td>Team membership</td><td>Read</td><td>Read your team membership information</td></tr>
<tr><td></td><td>Write</td><td>Read and write to your team membership information</td></tr>
<tr><td></td><td></td><td></td></tr>
<tr><td>Repositories</td><td>Read</td><td>Read your repositories</td></tr>
<tr><td></td><td>Write</td><td>Read and write to your repositories</td></tr>
<tr><td></td><td>Admin</td><td>Administer your repositories</td></tr>
<tr><td></td><td></td><td></td></tr>
<tr><td>Pull requests</td><td>Read</td><td>Read your repositories and their pull requests (overrides repositories)</td></tr>
<tr><td></td><td>Write</td><td>Read and write your repositories and their pull requests (overrides repositories)</td></tr>
<tr><td></td><td></td><td></td></tr>
<tr><td>Issues</td><td>Read</td><td>Read your repositories' issues</td></tr>
<tr><td></td><td>Write</td><td>Read and write to your repositories' issues</td></tr>
<tr><td></td><td></td><td></td></tr>
<tr><td>Wikis</td><td>Read and Write</td><td>Read and write to your repositories' wikis</td></tr>
<tr><td></td><td></td><td></td></tr>
<tr><td>Snippets</td><td>Read</td><td>Read your snippets</td></tr>
<tr><td></td><td>Write</td><td>Read and write to your snippets</td></tr>
<tr><td></td><td></td><td></td></tr>
<tr><td>Webhooks</td><td>Read and Write</td><td>Read and write to your repositories' webhooks</td></tr>
</table>

It is important to understand exactly what your applications permission requirements are.

<h3>Pipelines Bitbucket Permissions (scope)</h3>

Pipelines requires the following Bitbucket permissions:


<h5>read and modify your account information</h5>


Ability to change properties on the user's account.

* delete the authorizing user's account
* manage the user's groups
* manupilate a user's email addresses
* change username, display name and avatar


<h5>Read and modify your repositories and their pull requests</h5>

Gives the add-on write (not admin) access to all the repositories the authorizing user has access to. No distinction is made between public or private repos. This scope implies repository, which does not need to be requested separately. This scope alone does not give access to the pull requests API.

* push access over https
* fork repos


<h5>Read your team membership information</h5>

The ability to find out what teams the current user is part of. This is covered by the teams endpoint.

information about all the groups and teams I am a member or admin of


<h5>Read and modify your repositories' webhooks</h5>

Gives access to webhooks. This scope is required for any webhook related operation.

This scope gives read access to existing webhook subscriptions on all resources you can access, without needing further scopes. This means that a client can list all existing webhook subscriptions on repository foo/bar (assuming the principal user has access to this repo). The additional repository scope is not required for this.

Likewise, existing webhook subscriptions for a repo's issue tracker can be retrieved without holding the issue scope. All that is required is the webhook scope.

However, to create a webhook for issue:created, the client will need to have both the webhook as well as issue scope.

* list webhook subscriptions on any accessible repository, user, team, or snippet
* create/update/delete webhook subscriptions



<h3><a name="detailed-information"></a>Detailed Information</h3>

<h4><a name="repository"></a>repository</h4>

Gives the add-on read access to all the repositories the authorizing user has access to. Note that this scope does not give access to a repository's pull requests.

* access to the repo's source code
* clone over https
* access the the file browsing API
* download zip archives of the repo's contents
* the ability to view and use the issue tracker on any repo (created issues, comment, vote, etc)
* the ability to view and use the wiki on any repo (create/edit pages)

<h4><a name="repository"></a>repository:write</h4>

Gives the add-on write (not admin) access to all the repositories the authorizing user has access to. No distinction is made between public or private repos. This scope implies repository, which does not need to be requested separately. This scope alone does not give access to the pull requests API.

* push access over https
* fork repos

<h4><a name="repository"></a>repository:admin</h4>

Gives the add-on admin access to all the repositories the authorizing user has access to. No distinction is made between public or private repos. This scope does not imply repository or repository:write. It gives access to only the admin features of a repo, not direct access to its contents. Of course it can be (mis)used to grant read access to another user account who can then clone the repo, but repos that need to read of write source code would also request explicit read or write. This scope comes with access to the following functionality:

* view and manipulate committer mappings
* list and edit deploy keys
* ability to delete the repo
* view and edit repo permissions
* view and edit branch permissions
* import and export the issue tracker
* enable and disable the issue tracker
* list and edit issue tracker version, milestones and components
* enable and disable the wiki
* list and edit default reviewers
* list and edit repo links (JIRA/Bamboo/Custom)
* list and edit the repository web hooks
* initiate a repo ownership transfer

<h4><a name="snippet"></a>snippet</h4>

Gives the add-on read access to all the snippets the authorizing user has access to. No distinction is made between public and private snippets (public snippets are accessible without any form of authentication).

* view any snippet
* create snippet comments

<h4><a name="snippet-write"></a>snippet:write</h4>

Gives the add-on write access to all the snippets the authorizing user can edit. No distinction is made between public and private snippets (public snippets are accessible without any form of authentication). This implies the Snippet Read scope which does not need to be requested separately.

* edit snippets
* delete snippets

<h4><a name="issue"></a>issue</h4>

Ability to interact with issue trackers the way non-repo members can. This scope does not imply any other scopes and does not give implicit access to the repository the issue is attached to.

* view, list and search issues
* create new issues
* comment on issues
* watch issues
* vote for issues

<h4><a name="issue-write"></a>issue:write</h4>

This implies issue, but adds the ability to transition and delete issues. This scope does not imply any other scopes and does not give implicit access to the repository the issue is attached to.

* transition issues
* delete issues

<h4><a name="wiki"></a>wiki</h4>

Gives access to wikis. No distinction is made between read and write as wikis are always editable by anyone. This scope does not imply any other scopes and does not give implicit access to the repository the wiki is attached to.

* view wikis
* create pages
* edit pages
* push to wikis
* clone wikis

<h4><a name="pullrequest"></a>pullrequest</h4>

Gives the add-on read access to pull requests and collaborate on them. This scope implies repository, giving read access to the pull request's destination repository.

* see and list pull requests
* comment on pull requests
* approve pull requests
* create and resolve tasks

<h4><a name="pullrequest-write"></a>pullrequest:write</h4>

Implies pullrequest but adds the ability to create, merge and decline pull requests. This scope implies repository:write, giving write access to the pull request's destination repository. This is necessary to facilitate merging.

* merge pull requests
* decline pull requests
* create pull requests

<h4><a name="account-email"></a>account:email</h4>

Ability to see the user's primary email address. This should make it easier to use Bitbucket Cloud as a login provider to add-ons or external applications.

<h4><a name="account"></a>account</h4>

Ability to see all the user's account information. Note that this does not include any ability to mutate any of the data.

* see all email addresses
* language
* location
* website
* full name
* SSH keys
* user groups

<h4><a name="account-write"></a>account:write</h4>

Ability to change properties on the user's account.

* delete the authorizing user's account
* manage the user's groups
* manupilate a user's email addresses
* change username, display name and avatar

<h4><a name="team"></a>team</h4>

The ability to find out what teams the current user is part of. This is covered by the teams endpoint.

* information about all the groups and teams I am a member or admin of

<h4><a name="team-write"></a>team:write</h4>

Implies team, but adds the ability to manage the teams that the authorizing user is an admin on.

* manage team permissions

<h4><a name="webhook"></a>webhook</h4>

Gives access to webhooks. This scope is required for any webhook related operation.

This scope gives read access to existing webhook subscriptions on all resources you can access, without needing further scopes. This means that a client can list all existing webhook subscriptions on repository foo/bar (assuming the principal user has access to this repo). The additional repository scope is not required for this.

Likewise, existing webhook subscriptions for a repo's issue tracker can be retrieved without holding the issue scope. All that is required is the webhook scope.

However, to create a webhook for issue:created, the client will need to have both the webhook as well as issue scope.

* list webhook subscriptions on any accessible repository, user, team, or snippet
* create/update/delete webhook subscriptions

<h3><a name="confirming-permissions"></a>Confirming Permissions</h3>

You can use the following example python code to validate the permissions requested of bitbucket users.

~~~
from rauth import OAuth1Service

def get_session():
    # Create a new consumer at https://bitbucket.org/account/user/{username}/api
    CONSUMER_KEY = raw_input('Enter your Bitbucket consumer key: ')
    CONSUMER_SECRET = raw_input('Enter your Bitbucket consumer secret: ')

    # API URLs from https://confluence.atlassian.com/display/BITBUCKET/oauth+Endpoint
    REQUEST_TOKEN_URL = 'https://bitbucket.org/!api/1.0/oauth/request_token'
    ACCESS_TOKEN_URL = 'https://bitbucket.org/!api/1.0/oauth/access_token'
    AUTHORIZE_URL = 'https://bitbucket.org/!api/1.0/oauth/authenticate'

    # Create the service
    bitbucket = OAuth1Service(name='bitbucket',
                              consumer_key=CONSUMER_KEY,
                              consumer_secret=CONSUMER_SECRET,
                              request_token_url=REQUEST_TOKEN_URL,
                              access_token_url=ACCESS_TOKEN_URL,
                              authorize_url=AUTHORIZE_URL)


    # Change CALL_BACK to something that listens for a callback
    # with oauth_verifier in the URL params and automatically stores
    # the verifier.
    CALL_BACK = 'http://localhost?dump'

    # Make the request for a token, include the callback URL.
    rtoken, rtoken_secret = bitbucket.get_request_token(params={'oauth_callback': CALL_BACK})

    # Use the token to rquest an authorization URL.
    authorize_url = bitbucket.get_authorize_url(rtoken)

    # Send the user to Bitbucket to authenticate. The CALL_BACK is the
    # URL. The URL is redirected to after success. Normally, your
    # application automates this whole exchange.
    print 'Visit %s in new browser window.' % (authorize_url)

    # You application should also automated this rather than request
    # it from the user.
    oauth_verifier = raw_input('Enter oauth_verifier here: ')

    # Returns a session to Bitbucket using the verifier from above.
    return bitbucket.get_auth_session(rtoken, rtoken_secret, data={'oauth_verifier': oauth_verifier})


if __name__ == '__main__':
    session = get_session()

    username = raw_input('Enter a username: ')
    repo_slug = raw_input('Enter a repository slug: ')

    url = 'https://api.bitbucket.org/1.0/repositories/%s/%s/branches' % (username, repo_slug)
    resp = session.get(url)
    print resp.json()
~~~

Above source code from: <a href="https://confluence.atlassian.com/bitbucket/oauth-consumer-examples-338367592.html" target="_blank">OAuth consumer exapmle</a>.

<h3><a name="distelli-bitbucket-permissions"></a>Pipelines Bitbucket Permissions</h3>

Pipelines requires the following Bitbucket OAuth permissions for integration.

<table>
<tr><td><b>Type</b></td><td><b>Access</b></td><td><b>Purpose</b></td></tr>
<tr><td>Account</td><td>Write</td><td>Needed to post ssh keys.</td></tr>
<tr><td>Team Membership</td><td>Read</td><td>For future integrations.</td></tr>
<tr><td>Repositories</td><td>Write</td><td>Needed for ssh access to repositories.</td></tr>
<tr><td>Pull Requests</td><td>Write</td><td>For future integrations.</td></tr>
<tr><td>Issues and Snippets</td><td>Read</td><td>For future integrations.</td></tr>
<tr><td>Wikis</td><td>Write</td><td>For future integrations.</td></tr>
<tr><td>Webhooks</td><td>Read and Write</td><td>To post and delete webhooks.</td></tr>
</table>




