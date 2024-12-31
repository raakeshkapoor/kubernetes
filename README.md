# kubernetes
Kubernetes for All

# For Test Purpose

## Create a POD using Command

```sh
#Below mentioned command will create a POD by using Nginx image.
kubectl run web-server --image=nginx

#Show Label assigned to the POD by default.
#The below mentioned command will show run=web-server as a label
kubectl get pods --show-labels

#Remove the default label.
kubectl label pod web-server run-

#Assign a new Label to POD
kubectl label pod web-server owner=rakesh

# Verify if the Label is assigned or not
kubectl get pods --show-labels

# Assign label while creating a POD
kubectl run web-server --image=nginx --labels="owner=sales"

```

## Create a POD using File


```sh
apiVersion: v1
kind: Pod
metadata:
  name: web-server
spec:
  containers:
    - name: web-server
      image: nginx
```

### How to Access POD

```sh
kubectl exec -it web-server -- /bin/bash
```

**NOTE**: When we access the POD, it uses the POD name for the Container. 

### Define Custom Hostname for Container
- In the YAML file, node01 is the **custom-hostname**. Name assigned as the hostname is taking preference over other names.  

```sh
apiVersion: v1
kind: Pod
metadata:
  name: web-server
spec:
  hostname: node01 # custom-hostname
  containers:
    - name: node01
      image: nginx
```

### How to Access POD
- Once POD is accessed, run the command **hostname** to check the hostname of the container. 

```sh
kubectl exec -it web-server -- /bin/bash
```

### How to Create Multi-Container POD
- The below mentioned YAML file will create a POD with 2 containers i.e. Nginx-Container and Busybox-Container.

- Create a multi-container.yml file

```sh
vim multi-container.yml
```

- Copy and past the below mentioned code. 
```sh
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod
spec:
  containers:
    - name: nginx-container
      image: nginx:latest
      ports:
        - containerPort: 80
    - name: busybox-container
      image: busybox:latest
      command: ["sh", "-c", "while true; do echo Hello from Busybox; sleep 10; done"]
      ports:
        - containerPort:8080 
```
- Create the POD
```sh
kubectl apply -f multi-container.yml

kubectl get pods
```

### How to Access Multi-Container POD

- To execute commands in the nginx-container:
```sh
kubectl exec -it multi-container-pod -c nginx-container -- /bin/bash
```
- To execute commands in the busybox-container:
```sh
kubectl exec -it multi-container-pod -c busybox-container -- sh
```
