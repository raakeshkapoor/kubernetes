# POD Limit

For onpremises, POD limit for per Node is 110 i.e. we can create upto 110 PODs per NODE. However, we can change this number depending on our requirements. 

## Step 1. Check POD Limit

```sh
# This command will show 110, which is the default number of PODs we can create.  
kubectl describe node worker02 | grep -i pods

# Check the total number of PODs running on worker02 for all the Namespaces 
kubectl get pods --all-namespaces -o wide | grep -i worker02

# To see the count run the following command

kubectl get pods --all-namespaces -o wide | grep -i worker02 | wc -l

```

## Step 2. Reduce the maxPod for Testing

Follow these steps in any of the worker Node. For testing, we are following these steps on worker02

```sh
# Go to /var/lib/kubelet directory and edit config.yaml file.

cd /var/lib/kubelet

# Check for maxPods and change the value to 5. If it is not available then add the same as the last line
maxPods: 5

# Save the file and restart the kubelet service. Make sure we are following these steps on the Node on which we want to change the maxPods. In this example we are following all these steps on worker02.
systemctl restart kubelet


# Ceate PODS on worker2. We have assigned the Label on the worker02 and using NodeSelector to create all the PODs on worker02

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
      nodeSelector:
        team: sales

# As we try to create more PODs than we have specified, it will show them as Pending. 
# Run the describe command to see detail information. Replace the POD name, with pending POD name
kubectl describe pod <POD Name>

```

## Step 3. Revert the Changes

```sh

# Go to cd /var/lib/kubelet, edit config.yaml file to comment or rever the changes
cd /var/lib/kubelet
vim config.yaml

# After commenting maxPods, save the file and restart the kubelet service
systemctl restart kubelet

# This will create all the Pending PODs automatically

```
