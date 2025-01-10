# NodePort

## Command to Create a NodePort Service
```sh
# Create the deployment for both the Web-Server and DB-Server as created in ClusterIP and then create the NodePort Service. 
# Command to create the NodePort
kubectl expose deployment db-server --port=80 --type=NodePort

# Use the IP of the Node (Master or Worker) add the port number assigned by NodePort and access it from outside the Nodes. 
# It also creates a ClusterIP, which can be accessed from any other Node within cluster. 
# Try increasing the Replicas of the DB-Server YAML file and it will increase the EndPoints of NodePort. We can check the same by using the describe command. 
```
## File to Create a NodePort Service

```sh
# Create a nodeport.yml file (You can define any name)
vim nodeport.yml

# Paste the below mentioned Code in the nodeport.yml file. 

apiVersion: v1
kind: Service
metadata:
  name: lb
spec:
  selector:
    app: web-server
  ports:
    - port: 80
  type: NodePort

# Check the NodePort Service. It will show the Port, ClusterIP, EndPoints, etc. 
watch kubectl describe service <NodePort Service Name>

```
