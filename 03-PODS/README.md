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

### Deleate POD
- Deleate a Single POD
```
sudo kubectl delete pod <POD Name>
```

- Delete all the PODs
```
sudo kubectl delete pods --all
```

## Create PODs Using YAML File


