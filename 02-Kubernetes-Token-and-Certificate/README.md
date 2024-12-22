# Kubernetes Token and Certificate

Kubernetes Token is used to join the node to the Master to make it a Worker. 

## Command to Join Worker 
The command required to connect the Worker Node to Kubernetes is given below:
- Replace the Token and CA details based on the **kubeadm init** output

```
kubeadm join 192.168.1.160:6443 --token kkcj7q.rz5w3km4ciaeuxpv \
	--discovery-token-ca-cert-hash sha256:c5ab4e171b176ae1ed3457d7e86bb9e9272f55252947adabaca757ae3555
```

## Reset Worker
- ## This will make the worker Not-Ready but will not delete the Node. 
```
kubeadm reset -f
```
- ## Delete the Not-Ready Worker Node.
  Enter the Node Name that you wanted to Delete. 
```
kubeadm delete node <Node-Name>
```
- ## Re-Join Worker to Kubernetes
  -  Re-Join the Worker Node to Kubernetes by running the below mentioned command.
  -  Replace the Token with your Token and the CA with your CA details. 
```
kubeadm join 192.168.1.160:6443 --token 5ez717.q8q537t2tn4e98vg --discovery-token-ca-cert-hash sha256:3e5a51ca75a7184722e9bc36bcb90c62b220493c1b4a6bec940b359171b83031
```
- ## Verify Worker
```
kubectl get nodes
```

# Reset the Master
```
kubeadm  reset -f
```

# Initialize the Master Node again
kubeadm init

# To Join the Worker Node to Kubernetes by running the below mentioned command. Replace the Token with your Token and the CA with your CA details. 
kubeadm join 192.168.1.160:6443 --token 5ez717.q8q537t2tn4e98vg --discovery-token-ca-cert-hash sha256:3e5a51ca75a7184722e9bc36bcb90c62b220493c1b4a6bec940b359171b83031
