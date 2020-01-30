---
layout: default
title: "Groups"
---

Puppet Pipelines allows you to assign permissions, via groups, to users on your account. For more information on users see [Users](./users.html).

Permissions are assigned to users through groups. To assign a user permissions, the user must be [added to your Pipelines account](./users.html).

## Create a group

Creating a group allows you to provision permissions for your users. 

1. [Sign in to the Pipelines web UI](https://pipelines.puppet.com/login).
1. Click the <b>gear icon</b> on the top right.
1. Click the <b>Groups</b> link on the left.
1. Click the <b>Add Group</b> button.
1. Give the group a unique name.

    > **Note:** The name should be descriptive of the permissions. For example: 'read-only' or 'deploy-only'.

1. Optionally, enter a description for the group.
1. Set the group permissions. For an in-depth look at the permissions, see the **Assign Group Permissions** section.

    > **Note:** You must set all permissions before you can save the group.

1. At the bottom of the groups page you can add users to the group. Search for users either by their Pipelines username or their email.

    > **Note:** You can only add users to your group that are members of your account. For more information on users, see [User](./users.html).

1. When you are done, click the <b>Save Group</b> button.

## Assign group permissions

When creating a group, you must assign the group a set of permissions. You can edit the group and adjust permissions.

You can create more than one group. Users that are members of more than one group will receive the <b>least</b> restrictive permission of the groups they are in.

<h3><a name="permissions"></a>Permissions</h3>

<table width="642" cellpadding="4" cellspacing="0">
	<col width="15">
	<col width="100">
	<col width="316">
	<col width="177">
	<thead>
		<tr valign="top">
			<th colspan="2" width="123" bgcolor="#4fba6f" style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0.04in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Permission</p>
			</th>
			<th width="316" bgcolor="#4fba6f" style="border-top: 1px solid #000000; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0.04in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Description</p>
			</th>
			<th width="177" bgcolor="#4fba6f" style="border: 1px solid #000000; padding: 0.04in">
				<p>Requires</p>
			</th>
		</tr>
	</thead>
	<tbody>
		<tr valign="top">
			<td colspan="2" width="123" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><font color="#ffffff"><b>Applications</b></font></p>
			</td>
			<td width="316" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="177" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>Full Control</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Full access to all account applications</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>Read-only</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Can view all account applications</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>Deploy Only</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Can deploy account applications</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Environment access</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>Push Only</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Can push account applications (create releases)</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>No Access</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>No access to account applications</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
		</tr>
		<tr valign="top">
			<td colspan="2" width="123" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><font color="#ffffff"><b>Environments</b></font></p>
			</td>
			<td width="316" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="177" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>Full Control</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Full access to account environments</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Application access</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>Read-only</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Can view account environments</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Application access</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>Deploy Only</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Can deploy to account environment</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Application access</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>No Access</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>No access to account environments</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
		</tr>
		<tr valign="top">
			<td colspan="2" width="123" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><font color="#ffffff"><b>Agent</b></font></p>
			</td>
			<td width="316" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="177" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>Full Control</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Full control of the agent</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>No Access</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>No access to the agent</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
		</tr>
		<tr valign="top">
			<td colspan="2" width="123" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><font color="#ffffff"><b>Global Settings</b></font></p>
			</td>
			<td width="316" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="177" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>Full Control</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Can manage global account settings</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>Read-only</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Can view global account settings</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>No Access</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>No access to global account settings</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
		</tr>
		<tr valign="top">
			<td colspan="2" width="123" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><font color="#ffffff"><b>Credentials</b></font></p>
			</td>
			<td width="316" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="177" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>Full Control</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Can manage account credentials</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Global settings access</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>Read-only</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Can view account credentials</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Global settings access</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>No Access</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>No access to account credentials</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
		</tr>
		<tr valign="top">
			<td colspan="2" width="123" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><font color="#ffffff"><b>Billing &amp; Plans</b></font></p>
			</td>
			<td width="316" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="177" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>Full Control</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Can manage account billing and plans</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Global settings access</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>Read-only</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Can view account billing and plans</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Global settings access</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>No Access</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>No access to account billing and plans</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
		</tr>
		<tr valign="top">
			<td colspan="2" width="123" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><font color="#ffffff"><b>Users</b></font></p>
			</td>
			<td width="316" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="177" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>Full Control</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Can manage account membership</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Global settings access</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>Read-only</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Can view account membership</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Global settings access</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>No Access</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>No access to the account's account</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
		</tr>
		<tr valign="top">
			<td colspan="2" width="123" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><font color="#ffffff"><b>Groups</b></font></p>
			</td>
			<td width="316" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="177" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>Full Control</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Can manage account groups</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Global settings access</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>Read-only</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Can view account groups</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>Global settings access</p>
			</td>
		</tr>
		<tr valign="top">
			<td width="15" bgcolor="#3b97d3" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
			<td width="100" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p align="right"><b>No Access</b></p>
			</td>
			<td width="316" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: none; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p>No access to account gorups</p>
			</td>
			<td width="177" bgcolor="#f0c419" style="border-top: none; border-bottom: 1px solid #000000; border-left: 1px solid #000000; border-right: 1px solid #000000; padding-top: 0in; padding-bottom: 0.04in; padding-left: 0.04in; padding-right: 0.04in">
				<p><br>
				</p>
			</td>
		</tr>
	</tbody>
</table>

## Edit group permissions

To edit a group, follow these steps.

<ol>
<li>In the Pipelines web UI, click the <b>gear icon</b> on the top right.</li>
<li>Click the <b>Groups</b> link on the left.</li>
<li>Click the group name for the group that you wish to edit. Note that you cannot change the name of an existing group.</li>
<li>Edit the group.</li>
<li>When you are done, click the <b>Save Group</b> button.</li>
</ol>

## Add users to a group

1. In the Pipelines web UI, click the <b>gear icon</b> on the top right.
1. Click the <b>Groups</b> link on the left.
1. Click the group name for the group that you wish to add users.
1. At the bottom of the groups page you can add user's to the group.
    Search for users either by their Pipelines username or their email.
1. When you are done, click the <b>Save Group</b> button.

## Remove users from a group

To remove users, follow these steps.

1. In the Pipelines web UI, click the <b>gear icon</b> on the top right.
1. Click the <b>Groups</b> link on the left.
1. Click the group name for the group that you wish to add users.
1. At the bottom of the groups page you can see the current user's in the group.
1. Click the <b>X</b> to the right of the user you wish to remove from the group.

    > **Note:** Deleting a group member will not remove that user from your account. The user may still have access to your Puppet Pipelines resources. Remove the user from your account to completely disallow access. For more information see [Removing Users](./users.html).

1. When you are done, click the <b>Save Group</b> button.

## Delete a group

Deleting a group will remove all the permissions from the users assigned to the group.

> **Note:** Deleting a group will not remove users from your account. Users may still have access to your Distell resources. Remove a user from your account to completely disallow access. For more information see [Removing Users](./users.html).

To delete a group, follow these steps.

<ol>
<li>In the Pipelines web UI, click the <b>gear icon</b> on the top right.</li>
<li>Click the <b>Groups</b> link on the left.</li>
<li>Click the <b>X</b> to the left of the group that you wish to remove.</li>
<li>When the dialog pops up asking you to confirm you want to delete the group, click the <b>Yes</b> button.
</ol>

