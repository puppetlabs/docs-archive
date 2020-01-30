---
layout: default
title: "Access management"
---

Use groups to assign permissions to users. 

Before you add a user to a group, make sure you've [added them to your Pipelines account](./users.html). You can add users to multiple groups. If a user belongs to more than one group, they receive the least restrictive permissions you've assigned them in any of the groups. 

Many of Pipeline's permission categories allow you to use overrides. For example, imagine you want to grant a user permission to deploy to a specific Kubernetes namespace called *dev*, but you want to grant them read-only access to the other available namespaces. You can set the **ClusterNamespace** to read-only and then set a **Permission Override** of **Deploy** for *dev*.

Pipelines permissions work in conjunction with Kubernetes role-based access control. For more information on setting up Kubernetes RBAC on your cluster, see [Set cluster permissions](./cluster-set-permissions.html). 

You can access **Groups** from **Settings** <img src="images/settings_cog.png" alt="Settings cog">.

Here are the permissions options for Pipelines for Containers groups.

<table width="642" cellpadding="4" cellspacing="0">
  <tr>
    <th colspan="2" align="left">Permission</th>
    <th align="left">Description</th> 
    <th align="left">Requires</th>
  </tr>
  <tr>
    <td colspan="4"><b>Agent</b></td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>Full Control</b></td>
    <td>Full control of the agent</td>
    <td></td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>No Access</b></td>
    <td>No access to the agent</td> 
    <td></td>
  </tr>
  <tr>
    <td colspan="4"><b>Settings</b></td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>Full Control</b></td>
    <td>Can manage global account settings</td>
    <td></td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>Read Only</b></td>
    <td>Can view global account settings</td>
    <td></td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>No Access</b></td>
    <td>No access to global account settings</td>
    <td></td>
  </tr>
  <tr>
    <td colspan="4"><b>Billing</b></td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>Full Control</b></td>
    <td>Can manage account billing and plans</td>
    <td>Global settings access</td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>Read Only</b></td>
    <td>Can view account billing and plans</td>
    <td>Global settings access</td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>No Access</b></td>
    <td>No access to account billing and plans</td>
    <td></td>
  </tr>
  <tr>
    <td colspan="4"><b>Credentials</b></td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>Full Control</b></td>
    <td>Can manage account credentials</td>
    <td>Global settings access</td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>Read Only</b></td>
    <td>Can view account credentials</td>
    <td>Global settings access</td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>No Access</b></td>
    <td>No access to account credentials</td>
    <td></td>
  </tr>
  <tr>
    <td colspan="4"><b>Cluster</b></td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>Full Control</b></td>
    <td>Can manage all Kubernetes clusters</td>
    <td></td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>Deploy</b></td>
    <td>Can deploy to all Kubernetes clusters</td>
    <td></td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>Read Only</b></td>
    <td>Can view all Kubernetes clusters</td>
    <td></td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>No Acccess</b></td>
    <td>No access to Kubernetes clusters</td>
    <td></td>
  </tr>
  <tr>
    <td colspan="4"><b>ClusterNamespace</b></td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>Full Control</b></td>
    <td>Can manage all Kubernetes cluster namespaces</td>
    <td></td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>Deploy</b></td>
    <td>Can deploy to all Kubernetes cluster namespaces</td>
    <td></td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>Read Only</b></td>
    <td>Can view all Kubernetes cluster namespaces</td>
    <td></td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>No Acccess</b></td>
    <td>No access to Kubernetes cluster namespaces</td>
    <td></td>
  </tr>
  <tr>
    <td colspan="4"><b>Users</b></td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>Full Control</b></td>
    <td>Can manage account membership</td>
    <td>Global settings access</td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>Read Only</b></td>
    <td>Can view account membership</td>
    <td>Global settings access</td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>No Access</b></td>
    <td>No access to the account's account</td>
    <td></td>
  </tr>
  <tr>
    <td colspan="4"><b>Groups</b></td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>Full Control</b></td>
    <td>Can manage account groups</td>
    <td>Global settings access</td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>Read Only</b></td>
    <td>Can view account groups</td>
    <td>Global settings access</td>
  </tr>
  <tr>
    <td width="15"></td>
    <td align="right"><b>No Access</b></td>
    <td>No access to account groups</td>
    <td></td>
  </tr>
</table>

Related topics: [Set cluster permissions](./cluster-set-permissions.html), [cluster namespaces](./cluster-namespace.html). 