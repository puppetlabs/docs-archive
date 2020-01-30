---
layout: default
title: "kubectl run Puppet Container Registry with K8S"
---

This document will walk through getting you started running Puppet Container Registry Docker Registry containers with Kubernetes from the command line on google cloud.


<h3>Requirements</h3>

Puppet Container Registry requires a large store to store docker layers. Currently Puppet Container Registry supports AWS S3 and Filesystem. The example in this document will use Filesystem with a persistent disk.

Puppet Container Registry requires a database. This must be MySQL 5.7. The example in this document will use the mysql/mysql:5.7 docker image.


<h3>Cluster</h3>

First you will need a Kubernetes cluster to run the containers. This document will use Google Cloud Platform SDK to create the cluster if it does not exist. You can also use an existing cluster.

This assume you have already downloaded and authenticated the <b>gcloud</b> suite of tools. For more information see <a href="https://cloud.google.com/sdk/" target="_blank">Google Cloud SDK</a>.

To create a cluster named <b>europa</b>:

~~~
gcloud container clusters create europa --machine-type n1-standard-1 --num-nodes 3 --zone us-central1-a --project YOUR_GOOGLE_PROJECT
~~~

The above will create a kubernete cluster:
<ul>
  <li>Called <b>europa</b>.</li>
  <li>In the <b>YOUR_GOOGLE_PROJECT</b> Google project.</li>
  <li>With <b>3</b> nodes of machine type <b>n1-standard-1</b>.</li>
  <li>In zone <b>us-central1-a</b>.</li>
</ul>

You may opt to change values appropriate for your environment. Realize any values you use may incur charges in your Google project.

You will need to get the credentials for this new cluster to manage it and complete the balance of this document.

~~~
gcloud container clusters get-credentials europa --zone us-central1-a --project YOUR_GOOGLE_PROJECT
~~~


<h3>Persistent disks</h3>

For persistence of data and docker image layers you will need a persistent disk. To create a 500GB persistent disk named <b>europa</b> with the Google SDK:

~~~
gcloud compute disks create --size 500GB europa --zone us-central1-a --project YOUR_GOOGLE_PROJECT
~~~

Ensure you use the same zone as your cluster.

> **Note:** You may <b>not</b> need to format the new blank disk.

<h3>Namespaces</h3>

The Kubernetes deployment specification exemplified further in this document specifically uses namespace <b>europa</b>.

To create the <b>europa</b> namespace with kubectl:

~~~
kubectl create namespace europa
~~~


<h3>Secrets</h3>

Several environment variables need to be available to initialize MySQL and europa. These will be implemented as secrets. The secrets will be created in the <b>europa</b> namespace. 

To create secrets with kubectl we will add the secret value to a file whose name is the secret key. For example, if we want a secret of <code>username=alex</code> we would create a file called <b>username</b> in which the only contents would be <b>alex</b>. For example:

~~~
echo -n 'alex' > ./username
kubectl create secret generic example --namespace europa --from-file=./username
~~~

The above will create a set of secrets in a cluster called <b>example</b> which will have only one key=value: username=alex.

Run the following commands to create the secret key/value files for europa. Feel free to change the values to suite your environment.

~~~
echo -n 'mysql://127.0.0.1:3306/europaDB' > ./EUROPA_DB_ENDPOINT
echo -n 'distelli' > ./EUROPA_DB_USER
echo -n 'password' > ./EUROPA_DB_PASS
echo -n 'Pa55w0rd' > ./MYSQL_ROOT_PASSWORD
echo -n 'europaDB' > ./MYSQL_DATABASE
echo -n 'distelli' > ./MYSQL_USER
echo -n 'password' > ./MYSQL_PASSWORD
echo -n '127.0.0.1' > ./MYSQL_ROOT_HOST
~~~

<ul>
  <li><b>EUROPA_DB_ENDPOINT</b> - The endpoint to the MySQL instance. This is in the format of <code>mysql://ADDRESS:PORT/DATABASE</code><br>This example runs both containers in the same pod so services between the containers are available on localhost (127.0.0.1).</li>
  <li><b>EUROPA_DB_USER</b> - The user that has full access to the Puppet Container Registry Database. Set below as MYSQL_USER.</li>
  <li><b>EUROPA_DB_PASS</b> - The password for the above user. Set below as MYSQL_PASSWORD.</li>
  <li><b>MYSQL_ROOT_PASSWORD</b> - Set this to whatever you feel is appropriate.</li>
  <li><b>MYSQL_DATABASE</b> - This is the name of the database that Puppet Container Registry will use. On first deploy, this database will be created.</li>
  <li><b>MYSQL_USER</b> - This is the user name that has full access to the MYSQL_DATABASE created above. Also see EUROPA_DB_USER</li>
  <li><b>MYSQL_PASSWORD</b> - This is the password associated with the MYSQL_USER above. Also see EUROPA_DB_PASS</li>
  <li><b>MYSQL_ROOT_HOST</b> - This is the address that is allowed access to connect to the MySQL instance.</li>
</ul>

Now create the secrets in the kubernetes cluster with kubectl:

~~~
kubectl create secret generic europa --namespace europa \
--from-file=./EUROPA_DB_ENDPOINT \
--from-file=./EUROPA_DB_USER \
--from-file=./EUROPA_DB_PASS \
--from-file=./MYSQL_ROOT_PASSWORD \
--from-file=./MYSQL_DATABASE \
--from-file=./MYSQL_USER \
--from-file=./MYSQL_PASSWORD \
--from-file=./MYSQL_ROOT_HOST
~~~


<h3>Deployment</h3>

We are ready to create the europa deployment. Create a file named <code>europa-deploy.yaml</code>, paste the below text into the file, and save it. Keeping the indentation intact is critical.

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
          image: puppet/puppet-container-registry-enterprise:latest
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

Create the deployment in your cluster with kubectl:

~~~
kubectl create -f europa-deploy.yaml 
~~~

> **Note:** The first deployment with a new persistent disk will take at least 5 minutes or possibly more. 

<h3>Service</h3>

A load balancer service needs to be created to make the europa docker registry and web interface available. Create a file named <code>europa-service.yaml</code> and paste the below contents into the file and save it.

~~~
apiVersion: v1
kind: Service
metadata:
  name: europa
spec:
  ports:
    - name: http
      port: 80
      protocol: TCP
      targetPort: 80
    - name: https
      port: 443
      protocol: TCP
      targetPort: 443
  selector:
    deployment: europa
    run: europa
  type: LoadBalancer
~~~

Create the service in your cluster with kubectl:

~~~
kubectl create -f europa-service.yaml 
~~~



<h3>Validation</h3>

Some commands you can run to view and validate the running deployment.

<h5>Look at Deployments</h5>

~~~
$ kubectl get deployments --namespace europa
NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
europa    1         1         1            1           10m
~~~

The key thing to note here it the number of <b>AVAILABLE</b> should be equal to the number DESIRED.


<h5>Look at pods</h5>

~~~
$ kubectl get pods --namespace europa
NAME                      READY     STATUS    RESTARTS   AGE
europa-1727192276-p21g0   2/2       Running   2          10m
~~~


<h5>Inspect container logs</h5>

~~~
$ kubectl logs europa-1727192276-p21g0 -c mysql --namespace europa
Initializing database
Database initialized
MySQL init process in progress...
Warning: Unable to load '/usr/share/zoneinfo/iso3166.tab' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/zone.tab' as time zone. Skipping it.
mysql: [Warning] Using a password on the command line interface can be insecure.
mysql: [Warning] Using a password on the command line interface can be insecure.
mysql: [Warning] Using a password on the command line interface can be insecure.
mysql: [Warning] Using a password on the command line interface can be insecure.

/entrypoint.sh: ignoring /docker-entrypoint-initdb.d/*


MySQL init process done. Ready for start up.
~~~


~~~
$ kubectl logs europa-1727192276-p21g0 -c europa --namespace europa
2017-03-10 22:46:14.918 UTC:[INFO]:[main]:com.distelli.europa.guice.IndexFactoryProvider:DB schema initializing
2017-03-10 22:46:14.943 UTC:[INFO]:[main]:com.zaxxer.hikari.HikariDataSource:HikariPool-1 - Started.
2017-03-10 22:46:15.580 UTC:[INFO]:[main]:com.distelli.europa.guice.IndexFactoryProvider:DB schema initialized in 664.38977ms
2017-03-10 22:46:16.361 UTC:[INFO]:[main]:org.eclipse.jetty.util.log:Logging initialized @3362ms
2017-03-10 22:46:16.685 UTC:[INFO]:[WebServer]:org.eclipse.jetty.server.Server:jetty-9.2.6.v20141205
2017-03-10 22:46:16.726 UTC:[INFO]:[WebServer]:org.eclipse.jetty.server.handler.ContextHandler:Started o.e.j.s.ServletContextHandler@cb191ca{/,null,AVAILABLE}
2017-03-10 22:46:16.750 UTC:[INFO]:[WebServer]:org.eclipse.jetty.server.ServerConnector:Started ServerConnector@f57b9e1{HTTP/1.1}{0.0.0.0:80}
2017-03-10 22:46:16.790 UTC:[INFO]:[WebServer]:org.eclipse.jetty.server.ServerConnector:Started ServerConnector@ed3068a{SSL-HTTP/1.1}{0.0.0.0:443}
2017-03-10 22:46:16.792 UTC:[INFO]:[WebServer]:org.eclipse.jetty.server.Server:Started @3793ms
~~~


<h5>Inspect service</h5>

~~~
$ kubectl get services
NAME         CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
europa       10.7.215.220   104.179.91.78   80:32224/TCP,443:32509/TCP   1h
kubernetes   10.7.210.1     <none>          443/TCP                      1h
~~~

> **Note:** Important to note the <b>EXTERNAL-IP</b> of the europa service. You can use this to reach the europa deploy, in your browser.<br>
http://104.179.91.78

<h3>Use Puppet Container Registry</h3>

You can browse to the EXTERNAL-IP of the europa service to use europa.

<code>http://104.179.91.89</code>

Of note, if you followed the instructions exactly above, your <b>Storage</b> will be <b>File System</b> of <code>/europa/repo</code>.

You can follow the getting started instructions here: [Getting Started with Puppet Container Registry](./getting-started.html).


