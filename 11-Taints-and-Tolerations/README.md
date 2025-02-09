# 1.  Taints

Node affinity is a property of Pods that attracts them to a set of nodes (either as a preference or a hard requirement). Taints are the opposite -- they allow a node to repel a set of pods.

A taint applied to a node acts as a "repellent." It prevents pods from being scheduled on that node unless the pods explicitly "***tolerate***" the taint.

```sh
# Try creating PODs before and after the Taint is being used. 

# Command to apply Taint
kubectl taint node node1 key1=value1:NoSchedule

# No pods can be scheduled on worker02
kubectl taint node worker02 team=sales:NoSchedule

# Command to delete Taint
kubectl taint node node1 key1=value1:NoSchedule-

kubectl taint node worker02 team=sales:NoSchedule-
```

## 1.1. Key Point 

A toleration does not force the pod to be scheduled on the tainted node. It only allows the scheduler to consider that node as a valid placement. If there are other nodes without taints that satisfy the pod's requirements, the scheduler may still choose those nodes instead.

If you want to enforce scheduling on a specific node:
- **Use nodeSelector or node affinity alongside tolerations.**

## 1.2. Assign a Role to the NODES

```sh
# This command will assign a worker Role to Node worker02
kubectl label node worker02 node-role.kubernetes.io/worker=

# Verify the Role
kubectl get nodes

# Create web-server.yml and paste the below mentioned code.
vim web-server.yml

apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: web-server
  name: web-server
spec:
  replicas: 10
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

# Create deployment. This will schedule PODs on both the Worker Nodes.
kubectl apply -f web-server.yml

# Delete the Deployment
kubectl delete -f web-server.yml
```

```sh 

# Verify the Taint on the worker02 Node. This will show No Taints : <none>, as no Taint is assigned to this Node. 
kubectl describe node worker02 | grep -i Taint

# Assign Taint using Role to Worker02
kubectl taint nodes worker02 node-role.kubernetes.io/worker=true:NoSchedule

# Verify the Taint on the worker02 Node. This will show the Taint applied to the Node. 
kubectl describe node worker02 | grep -i Taint

# Create deployment. This will schedule PODs only on worker01 and not on worker02.
kubectl apply -f web-server.yml

# Delete the Deployment
kubectl delete -f web-server.yml

# Delete the Taint from Worker02
kubectl taint nodes worker02 node-role.kubernetes.io/worker-

# Delete the Role from Single Node
kubectl label node worker02 node-role.kubernetes.io/worker-

# Delete the Role from multiple Nodes when same Role is assigned
kubectl label nodes --all node-role.kubernetes.io/worker-
```

# 2.  Toleration

## 2.1. **Tolerations in a Pod:**

A toleration in a pod configuration allows the pod to "tolerate" the taint and be scheduled on the node, despite the taint.

Tolerations are applied to pods. Tolerations allow the scheduler to schedule pods with matching taints. Tolerations allow scheduling but don't guarantee scheduling: the scheduler also evaluates other parameters as part of its function.

Taints and tolerations work together to ensure that pods are not scheduled onto inappropriate nodes. One or more taints are applied to a node; this marks that the node should not accept any pods that do not tolerate the taints.

## 2.2. Demo 01

```sh
# Assign a label to the Node on which you want to test Tolerations. This would add the label team=sales to worker02
kubectl label node worker02 team=sales

# Verify the label
kubectl describe node worker02

# Create the 02-web-server.yml file and copy the code given below:
vim 02-web-server.yml

# Paste the below-mentioned code. Make sure no Taint is enabled. 
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: web-server
  name: web-server
spec:
  replicas: 10
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
      tolerations:
        - key: "team"
          operator: "Equal"
          value: "sales"
          effect: "NoSchedule"
      nodeSelector:
        team: sales         # Match the label assigned to the Node


# Create the Deployment and PODs. This will host all the PODs on worker02.
kubectl apply -f 02-web-server.yml

# Delete the deployment
kubectl delete -f 02-web-server.yml
```

## 2.3. Demo 01

```sh
# Now Enable the Taint on worker02
kubectl taint node worker02 team=sales:NoSchedule

# Create the Deployment and PODs. This will again host all the PODs on worker02.
kubectl apply -f 02-web-server.yml

# Delete the deployment
kubectl delete -f 02-web-server.yml

# Now create the 03-web-server.yml file and paste the below mentioned code.
vim 03-web-server.yml

apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: web-server
  name: web-server
spec:
  replicas: 10
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
      tolerations:
        - key: "team"
          operator: "Equal"
          value: "sales"
          effect: "NoSchedule"

# Create the Deployment and PODs. This will host PODs on both worker01 and worker02.
kubectl apply -f 03-web-server.yml

# If we run web-server.yml. It will create all the PODs on worker01.

# If we run 02-web-server.yml. It will create all the PODs on worker02.

```
