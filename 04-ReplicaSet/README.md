# ReplicaSet
A ReplicaSet ensures that a specified number of pod replicas are running at any given time. 

## Create the ReplicaSet
```sh
# Create a rs.yml file
vim rs.yml

# Copy and paste the below mentioned code in the rs.yml file.

apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      owner: rakesh
  template:
    metadata:
      name: node01
      labels:
        owner: rakesh
    spec:
      containers:
      - name: nginx
        image: nginx

# Run the kubectl command to create the ReplicaSet
kubectl apply -f rs.yml

# Check the PODs and RS created
kubectl get pods,rs --show-labels


```

# Labs
01. Try deleting all the PODs and then ReplicaSet will recreate all the PODs again.

```sh
kubectl delete pods --all
```

2. Create a POD by using command, assign it a label that RS is using to manage PODs. Then create PODs using the File and check now many PODs RS is creating. 
   
```sh
#Command to create a POD
kubectl run web-server --image=nginx --labels='owner=rakesh'

#Create a rs.yml file
vim rs.yml

#Paste the below mentioned code in the rs.yml file. 

apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      owner: rakesh
  template:
    metadata:
      name: node01
      labels:
        owner: rakesh
    spec:
      containers:
      - name: nginx
        image: nginx


```

03. Now delete the ReplicaSet and check if it also deletes the manually created POD where same Label was assigned as per the RS YAML file. 

04. Assign multiple Labels to PODs and Selector.
```sh
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      owner: rakesh
      app: nginx
  template:
    metadata:
      name: node01
      labels:
        owner: rakesh
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
```
6. Assign multiple Labels to the Selector and a single label to POD.

```sh
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      owner: rakesh
      app: nginx
  template:
    metadata:
      name: node01
      labels:
        owner: rakesh
    spec:
      containers:
      - name: nginx
        image: nginx
```
8. Create 2 ReplicaSets with different Names and use them to manage the same set of Labels. 

9. Create 2 ReplicaSet with the same name. 

10. Delete the ReplicaSet without deleting the PODs.

```sh
# In the below-mentioned command nginx is the name of the ReplicaSet.
kubectl delete rs nginx --cascade=orphan
```

