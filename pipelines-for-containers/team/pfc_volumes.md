---
layout: default
title: "Connect a container to a volume"
---

Use Kubernetes volumes to store information that you don't want to lose between container restarts.

Containers are ephemeral and always start in a clean state. If you update a container, or it crashes and the kubelet restarts it, you lose data stored in the container's directories. A Kubernetes volume is located on the pod instead of the container, and if you store the container’s data in it, the container can restart without losing the data. A volume has the same lifetime as its pod. 

Volumes also allow you to share data between containers. You can set up multiple containers in a pod to share the same volume.

Kubernetes supports many types of volumes. The type of volume you use depends on your cloud provider. For example, Google Cloud Project (GCP) uses persistent volumes, and Amazon Web Services (AWS) uses Elastic Block Store (EBS) volumes. 

> **Before you begin**: Before you connect a container to a volume in Pipelines, set up a volume on the target cluster through your cloud provider. See your cloud provider's documentation for instructions for setting up volumes.

In Pipelines, add a volume from the deployment screen when creating a deployment:

1. Add a volume to your deployment:
   1. Click **Volumes** > **Add Volume**.
   1. Enter a name for the volume.
   1. Select a volume type. 
   1. Fill in the remaining fields, according to your volume type and cloud provider.
1. Mount the volume to your container:
   1. Select your container.
   1. Click **Volume mounts** > **Add Volume Mount**.
   1. Select the volume.
   1. Enter a mount path. This is the path on the container where the volume will be mounted. For example, `/var/lib/mysql`.
   1. (Optional) Enter a sub-path. Specify a directory on the volume where you want to connect the container mount path. If you don't specify a sub-path, the mount will use the root directory on the volume. Use this value if you're planning to mount multiple paths to the same volume.
   1. Under **Read Only**, select **true** to mount the volume as read-only, or **false** to give the container read and write privileges on the volume.

Related topics:
* For more information about deployments, see [Set up a deployment](./deployment-set-up.md).
* For more information about available Kubernetes volume types, see <a href="https://kubernetes.io/docs/concepts/storage/volumes/" target="_blank">Volumes</a>.
* For more information about GCP persistent disks and volume claims, see <a href="https://cloud.google.com/compute/docs/disks/" target="_blank">Storage</a>.
* For more information about AWS volumes, see <a href="https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AmazonEBS.html" target="_blank">Amazon Elastic Block Store</a>.