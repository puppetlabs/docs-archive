---
layout: default
title: "Cluster namespaces"
--- 

Namespaces allow you to partition your cluster into separate logical areas.

With Pipelines, you can use role based access control (RBAC) to lock a namespace down to specific users. Namespaces dictate the different stages in your pipelines, so make sure you're using a naming convention like *application-stage*. For example, a pipeline for a WordPress site may use *wordpress-dev*, *wordpress-stage*, and *wordpress-prod* namespaces.    

For more information on Pipelines RBAC, see [Access management](./group.html)

## Create a namespace

To create a namespace in your cluster:

1. Log into your Pipelines account. 
1. Click on **Clusters** and select your cluster.
1. Click **Namespaces**.
1. Enter a name for your namespace: for example, *wordpress-dev*.
1. Click **Create Namespace**.

You have created a new namespace in your cluster.

## Remove a namespace

1. Log into your Pipelines account. 
1. Click on **Clusters** and select your cluster.
1. Click **Namespaces**.
1. Click the trash can next to the namespace you want to delete.

> **Note:** Some existing namespaces are critical to the Kubernetes system and should not be removed. Removing one could render your cluster unusable.

## Create namespace secrets

1. From your projects page, click on **Secrets**.
1. Select your cluster.
1. Click **Create New Secret**.
1. Select a namespace.
1. Enter a secret name.
1. Enter the secrets as key=value, one per line. For example:
   <img src="images/europak8s-create-new-secrets-europa.png" alt="Create new Secrets">
1. Click **Create Secret**.

You have created cluster namespace secrets. YOur secrets are available during deployment in the contaner **env** section of the Kubernetes deployment specification.

~~~
env:
  - name: EUROPA_DB_ENDPOINT
    valueFrom:
      secretKeyRef:
        key: EUROPA_DB_ENDPOINT
        name: europa
~~~

Here is an example deployment specification that leverages the above example secrets.

~~~
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: europa
  namespace: europa
spec:
  replicas: 1
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
    type: RollingUpdate
  template:
    metadata:
      labels:
        deployment: europa
        app: europa
    spec:
      containers:
        - name: europa
          image: distelli/europa:latest
          ports:
          - containerPort: 80
            hostPort: 80
            protocol: TCP
            name: http
          - containerPort: 443
            hostPort: 443
            protocol: TCP
            name: https
          env:
            - name: EUROPA_DB_ENDPOINT
              valueFrom:
                secretKeyRef:
                  key: EUROPA_DB_ENDPOINT
                  name: europa
            - name: EUROPA_DB_USER
              valueFrom:
                secretKeyRef:
                  key: EUROPA_DB_USER
                  name: europa
            - name: EUROPA_DB_PASS
              valueFrom:
                secretKeyRef:
                  key: EUROPA_DB_PASS
                  name: europa
          volumeMounts:
          - mountPath: /europa/repo
            name: europa-disk
            subPath: europa/repo
        - name: mysql
          image: mysql/mysql-server:5.7
          ports:
          - containerPort: 3306
            hostPort: 3306
            protocol: TCP
            name: mysql
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  key: MYSQL_ROOT_PASSWORD
                  name: europa
            - name: MYSQL_DATABASE
              valueFrom:
                secretKeyRef:
                  key: MYSQL_DATABASE
                  name: europa
            - name: MYSQL_USER
              valueFrom:
                secretKeyRef:
                  key: MYSQL_USER
                  name: europa
            - name: MYSQL_PASSWORD
              valueFrom:
                secretKeyRef:
                  key: MYSQL_PASSWORD
                  name: europa
            - name: MYSQL_ROOT_HOST
              valueFrom:
                secretKeyRef:
                  key: MYSQL_ROOT_HOST
                  name: europa
          volumeMounts:
          - mountPath: /var/lib/mysql
            name: europa-disk
            subPath: var/lib/mysql
      volumes:
      - name: europa-disk
        gcePersistentDisk:
          pdName: europa
      dnsPolicy: ClusterFirst
      restartPolicy: Always
~~~

## Edit secrets

1. From your projects page, click **Secrets**.
1. Select your cluster.
1. Click **Edit** next to the secret you want to edit.


## Delete secrets

1. From your projects page, click **Secrets**.
1. Select your cluster.
1. Click **trash can** next to the secret you want to delete.

**Related topics**: [Access management](./group.html), [set cluster permissions](./cluster-set-permissions.html).