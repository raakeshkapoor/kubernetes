# Labels

## Step 01
Create multiple Worker Nodes and create Deployment to create 10 Nodes. Check if Nodes are being distributed on both the Worker Nodes or not.

```sh
vim web-server.yml

# Paste the below-mentioned code in the web-server.yml file.

apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: web-server
  name: web-server
spec:
  replicas: 20
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


# Create deployment and PODs
kubectl apply -f web-server.yml

# Check the PODs
kubectl get pods -o wide

```

## Step 02

Delete all the PODs and assign Label to Node worker02. Assign the label 'team: sales'

```sh
# Delete an existing deployment
kubectl delete -f web-server.yml

# Assign Label to worker02
kubectl label node worker02 team=sales

# Replace the web-server.yml file with the below mentioned code.

apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: web-server
  name: web-server
spec:
  replicas: 20
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
      nodeSelector:
        team: sales

# Create the Deployment and PODs
kubectl apply -f web-server.yml

#This will create all the PODs on worker02 based on the Label assigned to worker02. Verify the PODs created
kubectl get all -o wide

# Delete the Deployment and PODs
kubectl delete -f web-server.yml


# Delete the Label from worker02
kubectl label node worker02 team-

```

## LAB

1. Modify the YAML file to include the label that doesn't exist and then try creating the PODs.
2. Assign the same label on multiple Nodes and try creating PODs.
3. Assign Labels to multiple Nodes
```sh
kubectl label node worker01 worker02 team=sales
```
4. Remove label from multiple Nodes
```sh
kubectl label node worker01 worker02 team-
```

5. Remove label from all the Nodes in the environment
```sh
kubectl label nodes --all team-
```
