---
layout: default
title: "Add a Google Kubernetes Engine cluster to Pipelines"
--- 

You can provision an existing Google Kubernetes Engine (GKE) cluster, or you can create one:

1. Log into your Pipelines account.
1. Click **Clusters**.
1. If you have a suitable cluster:
   1. Connect Google Compute Engine (GCE) to your account:
      1. Click **Connect GCE**.
      1. Click **Integratons**.
      1. Click **Google Cloud**.
      1. Click **Connect**.
      1. Click **Connect Google Cloud Platform**.
      1. Sign into your Google account.
      1. Click **Allow**.
   1. Add the cluster to your account:
      > **Important:** If you've set your cluster up to use Kubernetes RBAC, follow the instructions at [Connect to an RBAC-enabled Kubernetes cluster](./pfc_permissions_kube_rbac.html) to connect your cluster to Pipelines. 
            	
      1. Click **Add Existing Cluster**.
      1. Select **GKE** as your provider and click **Continue**.
      1. Click **Sync Clusters**. Select your cluster and click **Update All Projects**.
      1. Click **Close**.
      
1. Otherwise, to create a cluster and provision it:
   1. Click **New Google Container Engine Cluster**. Use the following settings:
        <table>
        <tr>
            <td><b>Zone</b></td>
            <td>Select any of the zones that correspond with your location.</td>
        </tr>
        <tr>
            <td><b>Machine Type</b></td>
            <td>n1-standard-1</td>
        </tr>
        <tr>
            <td><b>Number of nodes</b></td>
            <td>3</td>
        </tr>
        <tr>
            <td><b>Disk Size in GB</b></td>
            <td>100</td>
        </tr>
        <tr>
            <td><b>Kubernetes version</b></td>
            <td>Select the most recent release.</td>
        </tr>
        <tr>
            <td><b>Configure Tags</b></td>
            <td>Leave this field blank.</td>
        </tr>
        <tr>
            <td><b>Access Scopes</b></td>
            <td>Leave these on their default settings.</td>
        </tr>
        <tr>
            <td><b>Credentials</b></td>
            <td>Enter a username and password for the cluster. The password should be at least 16 characters long.</td>
        </tr>
        </table>
   1. Click **Launch.**
   1. Click **Monitor Launch** and wait for the cluster to finish provisioning.
   1. Set up permissions on your cluster. For more, see <a href="./cluster-set-permissions.html">Set cluster permissions</a>

>**Remember**: The Sync Clusters feature is specifically for synchronizing clusters in Google Cloud that are not using Kubernetes RBAC.