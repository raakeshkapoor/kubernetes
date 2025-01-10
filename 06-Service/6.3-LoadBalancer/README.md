# Load Balancer
MetalLB is the Load Balancer by Kubernetes. There are many other options that can be used as LoadBalancer. 

MetalLB is a load balancer implementation for Kubernetes in environments that do not natively support one. It supports both Layer 2 (ARP/NDP) and BGP (Border Gateway Protocol) configurations.

LoadBalancer also creates the NodePort and the ClusterIP. 

## How Load Balancer Communication Works

**1. LoadBalancer (Entry Point)**

**Purpose**: Exposes the Kubernetes service to external clients by providing a publicly accessible IP address.

**How It Works:**

The LoadBalancer forwards incoming traffic from its external IP (or hostname) to the appropriate Kubernetes node.
The forwarding is typically done to the NodePort of the service.

**2. NodePort (Intermediate Step)**

**Purpose:** Exposes the service on a specific port on each Kubernetes node, enabling the LoadBalancer to direct traffic to any node in the cluster.

**How It Works:**

When a service is of type LoadBalancer, it automatically creates a NodePort.
Each node listens on this NodePort, regardless of whether the service’s Pod is running on that node.
Traffic arriving at the NodePort on any node is forwarded to the ClusterIP of the service.

**3. ClusterIP (Internal Routing)**

**Purpose:** Provides a stable internal IP address for the service, used for routing traffic to the appropriate Pods.

**How It Works:**

The ClusterIP is a virtual IP that Kubernetes uses to distribute traffic to the service's backend Pods.
Traffic directed to the ClusterIP is load-balanced across all Pods that match the service’s selector.

## Communication Flow from External Client to POD

### Communication Flow in Detail
**Step 1: External Client → LoadBalancer**

- A client sends a request to the external IP of the LoadBalancer (e.g., http://loadbalancer-ip:80).

- The LoadBalancer forwards the request to a NodePort on one of the Kubernetes nodes.

**Step 2: LoadBalancer → NodePort**
- The LoadBalancer uses the NodePort (*e.g., port 30080*) to send traffic to one of the cluster nodes.
- The node receiving the traffic might or might not host the Pod associated with the service.

**Step 3: NodePort → ClusterIP**
- The node receiving the traffic forwards it to the service’s **ClusterIP**.
- The ClusterIP ensures that the traffic is distributed across all healthy Pods associated with the service.

**Step 4: ClusterIP → Pod**
- The service selects one of the Pods (using its label selector) and forwards the traffic to it.
- The selected Pod processes the request and sends the response back through the same path.


## Create the deployment for the Web-Server for which LoadBalancer is required and then create the LoadBalancer Service. 

```sh
# Web-Server Deployment

vim ws01.yml

# Paste the below mentioned Code

apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-server
  labels:
    app: web-server
spec:
  replicas: 1
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


# Command to create the LoadBalancer Service
# This will create the LoadBalancer Service but the External IP will be Pending.

kubectl expose deployment web-server --port=80 --type=LoadBalancer

# OR 

# Use the below mentioned YAML file to create the LoadBalancer
vim lb.yml

# Paste the below mentioned Code to lb.yml file.

apiVersion: v1
kind: Service
metadata:
  name: lb
spec:
  selector:
    app: db-server
  ports:
    - port: 80
      targetPort: 80
  type: LoadBalancer

```

## Steps to Deploy MetalLB:

1. Install MetalLB

```sh
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.14.9/config/manifests/metallb-native.yaml

```
2. Configure MetalLB

```sh
# Create a metallb.yml file
vim metallb.ym.

# Paste the below mentioned code to metallb.yml file. 
# This will create 2 PODs for MetalLB and assign the IP 192.168.1.100 to the LoadBalancer Service. You can define your own range. Make sure the range defined is available and can communicate with Kubernetes Cluster Nodes. 
---
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: ip-pool
  namespace: metallb-system
spec:
  addresses:
  - 192.168.1.100-192.168.1.110
  autoAssign: true
---
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: ip-pool
  namespace: metallb-system

# Apply the Configuration File
kubectl apply -f metallb.yml

```
3. Increase the Replica of the Web-Server and check the EndPoints of LoadBalancer by using the below mentioned command. 
```sh
watch kubectl describe service lb
```
