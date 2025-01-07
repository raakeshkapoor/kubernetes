# Deployment
Take help to create the Deployment YAML file or go to the below mentioned link: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/

## Create Deployment using Command

```sh
# Below-mentioned command will create a Deployment with the name of nginx and used nginx:1.20 as the image.
# By default, it will create one replica. 

kubectl create deployment nginx --image=nginx:1.20

# Define Replicas for Deployment. This will create 4 replicas
kubectl create deployment nginx --image=nginx:1.20 --replicas=4

# Scale-out Replicas
kubectl scale deployment nginx --replicas=6

# Scale-in Replicas
kubectl scale deployment nginx --replicas=3

# Change Image of Deployment
kubectl set image deployment nginx nginx=nginx:1.22.1

```
## Create Deployment Using YAML File

```sh
kubectl explain deployment

#OR

kubectl explain deployment --recursive | less

# Watch the Creation of Deployment, ReplicaSet and POD
watch kubectl get pods,rs,deployment -o wide --show-labels


#Create a new Deployment YAML File
vim dep.yml

#Copy and paste the below mentioned Deployment Code to the dep.yml file

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3   # This will create 3 Replicas
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
        image: nginx
        ports:
        - containerPort: 80


# Create the Deployment, ReplicaSet and PODs

kubectl apply -f dep.yml

```
## Scale-out or Scale-in PODs

```sh
# Create the YAML file to create 3 Replicas

vim dep.yml

#Copy and paste the below mentioned code.

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
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
        image: nginx
        ports:
        - containerPort: 80

# Run the following command to create PODs

kubectl apply -f dep.yml

# Scale-out
# Open the dep.yml file and change the replicas to 10

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 10
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
        image: nginx
        ports:
        - containerPort: 80

# Run the below mentioned command again
# This will increase the total number of PODs to 10 from 3. 
kubectl apply -f dep.yml


#Similarly we can scale-in the total number of PODs by modifying the YAML file.

```
## Create Deployment YAML File using Command

```sh
# It will create a dep.yml file for Deployment. Dry-run is used to show you the output but will not create the actual resources. 

kubectl create deployment nginx --image=nginx:1.20 --replicas=4 --dry-run=client -o yaml > dep.yml
```

## Rollout Updates

### Check the Rollout History

```sh

#The below-mentioned command will show all the Commands in history which are recorded. 
# If the commands are not recorded then you will not see any commands recorded

kubectl rollout history deployment <deployment Name>

```

- To Record the commands use the below mentioned command after very command that you run **--record**

```sh
#Example
kubectl apply -f dep.yml --record=true

# To change the Image of the Container, you can also run the below-mentioned command

kubectl set image deployment.apps/nginx-deployment nginx=nginx:1.14.1 --record=true


# To check if the commands are recorded or not run the below mentioned command
kubectl rollout history deployment <deployment Name>

# To view the revisions and their details, you can run the below-mentioned command

kubectl rollout history deployment <deployment Name> --revision=<revisionNumber>

#Example of Command is given below. In the below mentioned command nginx-deployment is the name of the deployment.
# By using the below-mentioned command, we can compare all the revisions and identify the revision to which we want to rollback.

kubectl rollout history deployment nginx-deployment --revision=2


#For example if we want to rollback to revision 2 then run the below-mentioned command 
#You can also watch the old Deployment number to check and verify. 
#If we don't define any revision then it goes to the prevision revision. 
kubectl rollout undo deployment nginx-deployment --to-revision=2

```

## Annotate Change-Cause of Revisions

```sh
# Annotate Change-Cause of Deployment
kubectl annotate deployment nginx kubernetes.io/change-cause="NGINX Version 1.22 Deployed"

# Overwrite an existing Annotation

kubectl annotate deployment nginx kubernetes.io/change-cause="NGINX Version 1.22 Deployed" --overwrite

```


## Pause/Resume the Rollout
- We can also pause the Rollout. If the Rollout is paused then we cannot deploy the Rollout. To pause the Rollout use the below-mentioned command

```sh
# To pause to Rollout
kubectl rollout pause deployment <deployment Name>

# To resume the Rollout
kubectl rollout resume deployment <deployment Name>
```

# Labs
1. Use the command to create a Deployment with 2 replicas.

2. Try deleting all the PODs
```sh
kubectl delete pods --all
```
3. Try deleting replicaSet 

```sh
kubectl delete rs <replicaSet Name>

```

4. Do we need to define a Label for Deployment?
5. Create a deployment file and create 6 replicas in it. Create the PODs. Modify the file to change the replicas to 4 and deploy it again. Now the total number of PODS running are 4. Now used the rollout undo command to go back to previous version and check how many PODs it will create. 


