# Init Container

Init containers is a specialized containers that run before app containers in a Pod. Init containers can contain utilities or setup scripts not present in an app image.

## Understanding init containers
A Pod can have multiple containers running apps within it, but it can also have one or more init containers, which are run before the app containers are started.

Init containers are exactly like regular containers, except:

Init containers always run to completion.
Each init container must complete successfully before the next one starts.

## Init Container Demo

```sh

# Run the Watch Command
watch kubectl get all -o wide --show-labels


# Create a POD file
vim pod.yml

# Paste the below mentioned code in the pod.yml file

apiVersion: v1
kind: Pod
metadata:
  name: pod
  labels:
    name: MyApp
spec:
  containers:
  - name: nginx
    image: nginx
  initContainers:
  - name: init-myservice
    image: busybox:1.28
    command: ['sh', '-c', "until nslookup myservice.$(cat /var/run/secrets/kubernetes.io/serviceaccount/namespace).svc.cluster.local; do echo waiting for myservice; sleep 2; done"]
  - name: init-mydb
    image: busybox:1.28
    command: ['sh', '-c', "until nslookup mydb.$(cat /var/run/secrets/kubernetes.io/serviceaccount/namespace).svc.cluster.local; do echo waiting for mydb; sleep 2; done"]

# Create the POD
kubectl apply -f pod.yml

# Check the Watch Terminal, it would show the Status as Init:0/2. This will not create the POD.
kubectl get -f myapp.yaml

# Check the Log of the init containers
kubectl logs pod -c init-myservice # Inspect the first init container


kubectl logs pod -c init-mydb      # Inspect the second init container

# Describe the POD
kubectl describe pod pod

```

## Create the Service to Complete the POD Creation

```sh
# Create the Service YAML file

vim service.yml

# Copy and paste the below mentioned code in the YAML file.
# You can create one service at a time to check how the Init Containers are working. 
---
apiVersion: v1
kind: Service
metadata:
  name: myservice
spec:
  ports:
  - protocol: TCP
    port: 80
    targetPort: 9376
---
apiVersion: v1
kind: Service
metadata:
  name: mydb
spec:
  ports:
  - protocol: TCP
    port: 80
    targetPort: 9377

# Create the Service
kubectl apply -f service.yml

# Once the Service is created, it will also create the POD. 
# Now describe the POD again to check the Status and Reason
kubectl describe pod pod

# OR
kubectl describe pods

```

