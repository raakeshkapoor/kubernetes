# 1 Kubernetes PODs

## 1.1 PODs Using Commands
### 1.1.1 Command to Create PODs
- Below mentioned command will create a POD with the name of web-server by using the image of NGINX. 

```sh
kubectl run web-server --image=nginx
```
### 1.1.2 List All the PODs
```sh
# List all the PODs running in Default Namespace
kubectl get pods

# Get IP Address of PODs and Name of Worker on which POD is running
kubectl get pods -o wide
```

### 1.1.3 Describe POD
- This will describe the POD
```sh
kubectl describe pod <POD Name>
```

### 1.1.4 List all the running Containers and their Count
```sh
kubectl get pods --all-namespaces -o jsonpath="{.items[*].spec['initContainers', 'containers'][*].image}" |\
tr -s '[[:space:]]' '\n' |\
sort |\
uniq -c

# Alternate Command
kubectl get pods --all-namespaces -o jsonpath="{.items[*].spec.containers[*].name}" | tr -s ' ' '\n' |sort |uniq -c

```

### 1.1.5 Delete POD
- Delete a Single POD
```sh
kubectl delete pod <POD Name>
```

- Delete all the PODs
```sh
kubectl delete pods --all
```

## 1.2 PODs Using YAML File

### 1.2.1 Check the Format of POD YAML File
```sh
# Run the command if you are not sure about the Format of the YAML file
kubectl explain pod
```
### 1.2.2 Create a YAML File to create a POD
```sh
# Step 01: Create the YAML File
sudo vim ws01.yml

# Step 02: Copy and Paste the Code
apiVersion: v1  
kind: Pod             # As we are creating POD, therefore POD is mentioned
metadata:             # Details of the POD. Metadata is the data about POD
  name: web-server    # Name of the POD
spec:
  containers:
    - name: web-server
      image: nginx

# Step 03: Save the File and Run the Command to Create the POD
kubectl apply -f ws01.yml

# Step 04: List all the PODs
kubectl get pods -o wide
```
### 1.2.3 Delete PODs

```sh
# ws01.yml is the file name that we have used while creating the PODs. Replace the name with the file name used to created the PODs. 
kubectl delete -f ws01.yml
```
## 1.3 Access POD/Container
### 1.3.1 Access POD with Single Container
```sh
kubectl exec -it web-server /bin/bash
```
# 1.3.2 Access POD with Multiple Containers
- If we have multiple containers and we want to access a single container then use the below mentioned command.
  - In the below-mentioned command the first **web-server** is the name of the POD.
  - The second **web-server** is the name of the container in the **Web-Server** POD.
  - We can check the name of the container by using the describe command i.e. **kubectl describe pod web-server**.
```sh
kubectl exec -it web-server -c web-server /bin/bash
```


