# 1.  Deployment Strategies

## 1.1. Rolling Deployment (Default)
It is the default method of deployment. Rolling update are based on the below mentioned options. They can be defined in numbers or in percentage. By default they both are 25%:
- maxSurge
- maxUnavailable

### 1.1.1. Deploy Rolling Update Strategy

```sh
# Create a New file

vim rollingupdate.yml


# Paste the below-mentioned code in the newly created YAML file.
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 20
  strategy:
    type: RollingUpdate     #Strategy is RollingUpdate
    rollingUpdate:
      maxSurge: 10%         #It can be replaced with numbers
      maxUnavailable: 10%   #It can be replaced with numbers
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.20
        ports:
        - containerPort: 80

# Create the Deployment, ReplicaSet and PODs
kubectl apply -f rollingupdate.yml


# Update the Container image in the YAML file such as:

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 20
  strategy:
    type: RollingUpdate     #Strategy is RollingUpdate
    rollingUpdate:
      maxSurge: 10%         #It can be replaced with numbers
      maxUnavailable: 10%   #It can be replaced with numbers
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.20.1
        ports:
        - containerPort: 80

# ReCreate the Deployment

kubectl apply -f rollingupdate.yml

# Now you will see the rolling update is working as per the Strategy defined. 

```

## 1.2. Recreate
- It recreates the ReplicaSet, which requires a down-time. 
- It creates the new ReplicaSet and leaves the old ReplicaSet with zero PODs in that. 

```sh
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 4
  strategy:
    type: Recreate  	
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.20.2
        ports:
        - containerPort: 80
```

## 1.3. Canary Deployment
It is the preferred method of deployment in most of the Organisations. 

Canary Deployment is not Blue and Green Deployment. Some people are confused between Canary Deployment and Blue & Green Deployment. 

In **Canary Deployment**, the new version (Green) is rolled out incrementally to a small subset of users while most traffic continues to go to the old version (Blue). Over time, traffic is shifted from Blue to Green as confidence in the new version increases.

In **Canary Deployment**:

Both environments (Blue and Green) may serve traffic simultaneously, but the older version (Blue) is still considered production until full traffic is shifted.


In **Blue-Green Deployment**, the environment actively serving traffic is considered the production environment. The labels "Blue" or "Green" are just placeholders that swap roles during deployment.


