---
layout: default
title: "Set up a cluster"
--- 

Before you create your containers, you need to set up a Kubernetes cluster on Google Cloud Platform (GCP). This is where your containers are hosted after you've built and deployed them. If you have a Kubernetes cluster on GCP already, you can provision it by adding it to your Pipelines account. After you've created your cluster, set up authentication to allow your Kubernetes cluster to communicate with your Pipelines account. Next, add namespaces to partition your cluster into separate logical areas.

## Prerequisites

If you don’t have a Pipelines account, go to <a href="https://pipelines.puppet.com/signup" target="_blank_">https://pipelines.puppet.com/signup</a> to create one. 

Make sure you’ve signed up for a <a href ="https://cloud.google.com/" target="_blank_">Google Cloud Platform</a> account and enabled the following APIs:

<ul>
    <li><a href="https://console.cloud.google.com/apis/library/iam.googleapis.com" target="_blank">Identity and Access Management</a></li>
    <li><a href="https://console.cloud.google.com/apis/library/cloudresourcemanager.googleapis.com" target="_blank">Cloud Resource Manager</a></li>
    <li><a href="https://console.cloud.google.com/apis/library/compute.googleapis.com" target="_blank">Compute Engine</a></li>
    <li><a href="https://console.cloud.google.com/apis/library/container.googleapis.com" target="_blank">Kubernetes Engine</a></li>
</ul>

## Provision a cluster

If you have a suitable Kubernetes cluster on GCP, you can provision it, or you can create an entirely new cluster:

<ol>
    <li>Log into your Pipelines account.</li>
    <li>If you have a suitable cluster:</li>
    <ol>
    	<li>Connect Google Compute Engine (GCE) to your account:</li>
    	<ol>
    	    <li>Click <b>Integratons</b>.</li>
    	    <li>Click <b>Google Cloud</b>.</li>
    	    <li>Click <b>Connect Google Cloud Platform</b>.</li>
    	    <li>Sign into your Google account.</li>
    	    <li>Click <b>Allow</b>.</li>
    	</ol>
    	<li>Add your cluster to your account:</li>
    	<ol>
    	    <li>Click <b>Clusters</b>.</li>
    	    <li>Click <b>Sync Clusters</b>.</li>
    	    <li>Select your GCP project</li>
    	    <li>Click <b>Update Selected Projects</b>.</li>
    	    <li>Click <b>Close</b>.</li>
    	</ol>	    
    </ol>
    <li>Otherwise, to create a cluster and provision it:
    <ol>
    <li>Click <b>New Google Container Engine Cluster</b>. Use the following settings:</li>
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
    <li>Click <b>Launch.</b></li>
    <li>Click <b>Monitor Launch</b> and wait for the cluster to finish provisioning.</li>
    </ol></li>
</ol>

## Set up authentication on your cluster

After you provision a cluster configure it to communicate with Pipelines.
<ol>
    <li>Open your <a href="https://console.cloud.google.com/" target="_blank">Google Cloud Platform</a> dashboard.</li> 
    <li>On the navigation menu, click <b>Kubernetes Engine</b>.</li>
    <li>Select your cluster and click <b>Edit</b>.</li>
    <li>Under <b>Legacy Authorization</b>, select <b>Enabled</b>.</li>
    <li>Click <b>Save</b> and wait for the cluster to update.</li>
</ol>

Your Pipelines project can now communicate with your Kubernetes cluster.

## Add a namespace

Now it’s time to add namespaces to your cluster. 

Namespaces partition your cluster into separate logical areas, making it easier to distinguish between different workloads. When you set up an automated pipeline, it is good practice to use a separate namespace for each stage. Use a naming convention like `<application_name>-<stage_name>`. For example, `wordpress-dev`.    

To create three namespaces:
1. Log in to your Pipelines account. 
1. Click on **Clusters** and select your cluster.
1. Click **Namespaces**.
1. Create a wordpress-dev namespace:
   1. Enter a name for your namespace: *wordpress-dev*.
   1. Click **Create Namespace**.
1. Create a wordpress-stage namespace:
   1. Enter a name for your namespace: *wordpress-stage*.
   1. Click **Create Namespace**.
1. Create a wordpress-prod namespace:
   1. Enter a name for your namespace: *wordpress-prod*.
   1. Click **Create Namespace**.


Now that you’ve added a namespace, you have a defined area on your cluster where you’ll deploy your containers. It’s time to [create your Docker containers](./gs_create_mysql_container.html).
