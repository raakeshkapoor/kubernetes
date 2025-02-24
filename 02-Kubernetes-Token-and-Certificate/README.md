# Topic 01: Reset Master and Worker

Kubernetes Token is used to join the node to the Master to make it a Worker. 

### Command to Join Worker 
The command required to connect the Worker Node to Kubernetes is given below:
- Replace the Token and CA details based on the **kubeadm init** output

```sh
kubeadm join 192.168.1.160:6443 --token kkcj7q.rz5w3km4ciaeuxpv \
	--discovery-token-ca-cert-hash sha256:c5ab4e171b176ae1ed3457d7e86bb9e9272f55252947adabaca757ae3555
```

### Reset Worker
- This will make the worker Not-Ready but will not delete the Node. 
```sh
kubeadm reset -f
```
- Delete the Not-Ready Worker Node.
  Enter the Node Name that you wanted to Delete. 
```sh
kubectl delete node <Node-Name>
```
- Re-Join Worker to Kubernetes
  -  Re-Join the Worker Node to Kubernetes by running the below mentioned command.
  -  Replace the Token with your Token and the CA with your CA details. 
```sh
kubeadm join 192.168.1.160:6443 --token 5ez717.q8q537t2tn4e98vg --discovery-token-ca-cert-hash sha256:3e5a51ca75a7184722e9bc36bcb90c62b220493c1b4a6bec940b359171b83031
```
- Verify Worker
```sh
kubectl get nodes
```

### Reset the Master
- Resetting Master will remove all the etcd database, certificates, etc. All the data of Master, Worker, PODs, etc will be deleted. All the Worker are required to re-join the Kubernetes and all the PODs are required to be re-created. 
```sh
kubeadm  reset -f
```

-  Initialize the Master Node again
```sh
kubeadm init
```
- Verify all the NODEs
	- This command will only show the newly initialized Master Node. It will not show the Worker Nodes.  	
```sh
kubectl get nodes
```

- Re-join Worker to kubernetes
	- Worker Node was joined to the old Kubernetes Cluster and it still has details of the old Kubernetes Cluster. 
	- To re-join the Worker to Kubernetes, it is required to reset the Worker Node.
 	- If we try to reset the Worker without resetting then it will show an error message.
  	- Follow the below mentioned command to first reset the Worker and then re-join to Kubernetes.
  ```sh
  # Command to reset the Worker Node. Run this command on the Worker Node
  kubectl reset -f

  # Re-Join the Worker to Kubernetes. Replace the Token and CA based on the output of kubeadm init command. 
  kubeadm join 192.168.1.160:6443 --token 5ez717.q8q537t2tn4e98vg --discovery-token-ca-cert-hash sha256:3e5a51ca75a7184722e9bc36bcb90c62b220493c1b4a6bec940b359171b83031
  ```  
 
# Topic 02: Kubernetes Token and Certificate
## Tokens
### List Tokens
Newly created default Tokens are only available for 24 hrs. After 24 hrs, Tokens will get deleted automatically. Therefore, if you don't see a Token then it means that the Token is past 24 hrs and is deleted. 
```sh
kubeadm token list
```

### Create a New Token
Default token will be created for 24 hours if no TTL is mentioned. 
```sh
kubeadm token create
```
### Create a Token with TTL (Total Time to Live)
```sh
# Below mentioned Command will create a Token with TTL of 30 m
kubeadm token create --TTL 30m

# Below mentioned Command will create a Token with TTL of FOREVER
kubeadmin token create --TTL 0
```

### Delete a Token
```sh
# The below mentioned command is to delete the Token. Copy and paste the Token Name that you wanted to Delete. 
kubeadm token delete <token_Name>
```
## Certifiates
```
# To retrieve an existing Certificate run the below mentioned command
openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'
```

### Skip CA Verification
- Join the Worker to Master by skipping CA Verification.
- Running this command in the Production Environment is not recommended.

```
# The below-mentioned command can be used to join the Worker to Kubernetes without CA verification but it is not recommended. 
kubeadm join --token abcdef.1234567890abcdef --discovery-token-unsafe-skip-ca-verification 192.168.1.160:6443
```

# Topic 03: Join Kubernetes and Approvals
## Join Kubernetes
### Join Worker using a File
```
sudo kubeadm join --discovery-file path/to/file.conf (local file)
sudo kubeadm join --discovery-file https://url/file.conf (remote HTTPS URL)
```
### Retrieve Kubeadm Join command to join a Worker to Kubernetes

```sh
# If you are not sure about the complete kubeadm join command then run the following command on the Master to get the complete join command with Token and CSR.
# This command also creates a new Token

sudo kubeadm token create --print-join-command
```
## Manual Vs Auto Approval
### Auto Approval List
```sh
# To see the list of auto-approval requests received by Master from Workers, run the below-mentioned command

kubectl get csr
```
### Turn Off Auto Approval

```sh
# Go to the Master Node and run the following command
kubectl delete clusterrolebinding kubeadm:node-autoapprove-bootstrap

# Use the below mentioned command to check the Pending requests for CSR approval
watch kubectl get csr

# To check from where the request has come and more details
kubectl describe csr <CSR_Name>

# To approve the CSR request to join the Worker to the Kubernetes
kubectl certificate approve <CSR Name>

```


