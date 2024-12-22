# Kubernetes PODs

## Create PODs Using Commands
### Command to Create PODs
- Below mentioned command will create a POD with the name of web-server by using the image of NGINX. 

```
sudo kubectl run web-server --image=nginx
```
### List All the PODs
```
# List all the PODs running in Default Namespace
sudo kubectl get pods

# Get IP Address of PODs and Name of Worker on which POD is running
sudo kubectl get pods -o wide
```

### Describe POD
- This will describe the POD
```
sudo kubectl describe pod <POD Name>
```

### List all the running Containers and their Count
```
kubectl get pods --all-namespaces -o jsonpath="{.items[*].spec['initContainers', 'containers'][*].image}" |\
tr -s '[[:space:]]' '\n' |\
sort |\
uniq -c

# Alternate Command
kubectl get pods --all-namespaces -o jsonpath="{.items[*].spec.containers[*].name}" | tr -s ' ' '\n' |sort |uniq -c

```

### Delete POD
- Delete a Single POD
```
sudo kubectl delete pod <POD Name>
```

- Delete all the PODs
```
sudo kubectl delete pods --all
```

## Create PODs Using YAML File

### Check the Format of POD YAML File
```
# Run the command if you are not sure about the Format of the YAML file
sudo kubectl explain pod
```
### Create a YAML File to create a POD
```
sudo vim ws01.yml

# Copy and Paste the Code
apiVersion: v1  
kind: Pod             # As we are creating POD, therefore POD is mentioned
metadata:             # Details of the POD. Metadata is the data about POD
  name: web-server    # Name of the POD
spec:
  containers:
    - name: web-server
      image: nginx

```
### Save the File and Run the Command to Create the POD
```
# Save the file and run the command to create the POD
sudo kubectl apply -f ws01.yml
```





