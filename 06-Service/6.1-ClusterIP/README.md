# Cluster IP
It can only be accessed within the Cluster. It cannot be accessed outside the Cluster. Steps to create the Cluster IP is given below:

Scenaior: We'll create two deployments with the name of Web-Server and DB-Server. We'll login to Web-Server POD and then try to access DB-Server POD using the Cluster IP. For DB-Server we'll use NGINX image and not the actual Database. We'll delete the DB-Server POD and it will be recreated automatically. We'll again try to access the ClusterIP Service while sitting in the Web-Server. 

01. Create a new deployment for Web-Server and for DB-Server. 
```sh
# Web-Server Deployment

vim ws01.yml

# Paste the below mentioned Code

apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-server
  labels:
    app: web-server
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web-server
  template:
    metadata:
      labels:
        app: web-server
    spec:
      containers:
      - image: nginx
        name: nginx


# DB-Server Depolyment

vim db01.yml

# Paste the below mentioned Code

apiVersion: apps/v1
kind: Deployment
metadata:
  name: db-server
  labels:
    app: db-server
spec:
  replicas: 1
  selector:
    matchLabels:
      app: db-server
  template:
    metadata:
      labels:
        app: db-server
    spec:
      containers:
      - image: nginx
        name: nginx

```
02. Create a ClusterIP Service for DB-Server Deployment

There are two ways of creating a ClusterIP Service, you can try both:

2.1. Create ClusterIP Service using the Command

```sh
# DB-Server is the name of the deployment in the below-mentioned command. 

kubectl expose deployment db-server --port=80 --target-port=80
```

2.2. Create ClusterIP Service for DB-Server using the file. 

```sh
# Create a db-server.yml file.
vim service-db-server.yml

# Paste the below mentioned code in the db.yml file.
apiVersion: v1
kind: Service
metadata:
  labels:
    app: db-server
  name: service-db-server
spec:
  type: ClusterIP
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
  selector:
    app: db-server

```
03. Login to Web-Server POD and try to access ClusterIP Service created for the DB-Server

```sh
# Command to login to Web-Server POD
kubectl exec -it <web-server-POD-Name> -- /bin/bash

# Install the Curl command on the Web-Server POD

apt-get update -y
apt-get install curl -y

# Access the ClusterIP created for the DB-Server

curl <clusterIP>

```

04. Delete the DB-Server POD. It will re-create the DB-Server POD with the new IP address. Now try to access the ClusterIP again from the Web-Server. 

```sh
kubectl delete pod <db-server pod name>

```
05. Use the Describe Command for the Service service-db-server and check for the EndPoints. Now increase the replicas of the DB-Server. It will also increase the EndPoints of the service-db-server. 

```sh
watch kubectl describe service service-db-server

# Modify the db01.yml file to increase the replica to 4 and then run the kubectl apply command again. This will increase the EndPoint of the service to 4. 

kubectl apply -f db01.yml

```
06. Try accessing the ClusterIP from the Worker.
