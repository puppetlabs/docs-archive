---
layout: default
title: "Create a secret"
---

A secret is a Kubernetes object that lets you safely store small amounts of sensitive information like passwords or SSH certificates. 

Use a secret to avoid accidentally exposing sensitive information. Putting the password in a secret encodes it, and gives you control over where the password is used and who has access to it. For example, if you use a plain-text password in a container image, a user with access to your cluster could view the pod description for that container and read your password. Placing the password in a secret prevents this type of security exposure.

> **Tip**: Kubernetes secrets use Base64 encoding. 
  
In Pipelines, you can create secrets and pass them into containers as environment variables at the deployment stage. Container environment variables are lists of key-value pairs. With secret variables, only the value part of the pair is encoded, and not the key. You can store multiple key-value pairs in a secret and call a specific pair using `valueFrom`.

To create a secret:
1. Click **Clusters** and select your cluster.
1. Click **Secrets** > **Create New Secret**.
1. Select a namespace.
1. Enter a descriptive name for your secret. For example, `mysql-secrets`.
1. Enter the name of the key. For example, `MYSQL_ROOT_PASSWORD`.
1. Enter the value. For example, `my_password123`. This is the part of the key that is encoded.
1. (Optional) To add another key-value pair to the same secret, click **New Secret Data** and enter the key and value. Repeat as needed. 
1. Click **Create Secret**.

You pass secrets to containers during the deployment process. See [Set up a deployment](./deployment-set-up.md). 

To add a secret to a container:
1. From the **New Deployment** screen, select the container.
1. Click **Environment Variables** > **Add Variable**. 
1. Configure the following settings:
   * **Environment Variable Name**: A name for the environment variable. Use the same string for key below. For example, MYSQL_ROOT_PASSWORD
   * **Type of Variable**: Select **secretKeyRef**. The `secretKeyRef` key references a secret key-value pair in a Kubernetes pod description.  
   * **name**: Select the secret that you created above. For example, `mysql-secrets`
   * **key**: Select the key you want to add. For example, `MYSQL_ROOT_PASSWORD`.
1.  Click **Save Variable**.
1. (Optional) Click **Add Variable** if you want to add another secret.

Here's an example of a secret in a container's pod description:

```
env:
  -
    name: MYSQL_ROOT_PASSWORD
    valueFrom:
      secretKeyRef:
        name: gs-mysql-secret
        key: MYSQL_ROOT_PASSWORD

```    

Related topics:
* For more information about deployments, see [Set up a deployment](./deployment-set-up.md).
* For more information about containers, see [Containers overview](./container.gs)
* For information about setting up a pipeline, see [Set up a multi-stage pipeline](./pipeline-set-up.md)