---
layout: default
title: "Pipelines API tokens"
---

<br>
Before beginning these steps, ensure you are in the appropriate account. If you need to change accounts, see [Switching accounts](./users.md).

<h3>Create an API Token</h3>

1. In the Pipelines web UI click the **gear** icon on the top right.
2. Click the **API** link on the left.
3. Click the **Create API Token** button.
4. Give the token a meaningful **Description**.
5. Click the **Create Token** button.

You have created an API token. You can now test the token with the following ListApps call.

~~~
curl -i "https://api.distelli.com/USERNAME/apps?apiToken=TOKEN"
~~~

Replace **USERNAME** with your Pipelines user name or team user name.
Replace **TOKEN** with the Pipelines API token.

You should be returned a list of applications. If you do not get a **200 OK**, ensure you are using the correct user name with the correct token.

If necessary, see [Finding your Pipelines username](./account.html).

<br>
Before beginning these steps, ensure you are in the appropriate account. If you need to change accounts, see [Switching accounts](./users.md).

<h3>Deactivate an API Token</h3>

<ul>
<li>In the Pipelines web UI click the <b>gear</b> icon on the top right.</li>
<li>Click the <b>API</b> link on the left.</li>
<li>For the API token you wish to deactivate, click the <b>gear</b> on the right.</li>

<p>This will open a dialog.</p>

<li>Check the <b>Deactivate API Token</b>.</li>
<li>Click <b>Update Token</b> button.</li>
</ul>

You have deactivated your API token.

<br>
Before beginning these steps, ensure you are in the appropriate account. If you need to change accounts, see [Switching accounts](./users.md).

<h3>Delete an API Token</h3>

A token must be deactivated before it can be deleted.

<ol>
<li>In the Pipelines web UI click the <b>gear</b> icon on the top right.</li>
<li>Click the <b>API</b> link on the left.</li>
<li>For the API token you wish to delete, click the <b>gear</b> on the right.</li>

<p><b>Note:</b>The token <b>must be deactivated</b> before deleting.</p>

<li>Check the <b>I understand this is permanent</b> box.</li>
<li>Click <b>Delete Token</b> button.</li>
</ol>

You have permanently deleted your API token.

<br>
Before beginning these steps, ensure you are in the appropriate account. If you need to change accounts, see [Switching accounts](./users.md).

<h3>Find your API Token</h3>

> **Important:** You must have [created an API token](./api-token.html) before you can find it.

1. In the Pipelines web UI click the **gear** icon on the top right.
2. Click the **API** link on the left.

Here you can see any created API tokens.

If there are no tokens, you can create one. See [creating an API token](./api-token.html).

<br>
Before beginning these steps, ensure you are in the appropriate account. If you need to change accounts, see [Switching accounts](./users.md).

<h3>Reactivate an API token</h3>

These instructions assume the token you wish to reactivate has been deactivated.

<ol>
<li>In the Pipelines web UI, click the <b>gear</b> icon on the top right.</li>
<li>Click the <b>API</b> link on the left.</li>
<li>For the API token you wish to reactivate, click the <b>gear</b> on the right.</li>

<p>This will open a dialog.</p>

<li>Check the <b>Reactivate API Token</b>.</li>
<li>Click <b>Update Token</b> button.</li>
</ol>

You have reactivated your API token.