# Set Resource Quota for the PODs

A 'resource quota', defined by a ResourceQuota object, provides constraints that limit aggregate resource consumption per namespace. It can limit the quantity of objects that can be created in a namespace by type, as well as the total amount of compute resources that may be consumed by resources in that namespace. It can restrict consumption and creation of cluster resourcessuch as CPU time, memory, and persistent storage.

- For cpu and memory resources, 'ResourceQuotas' enforce that every (new) pod in that namespace sets a limit for that resource. If you enforce a resource quota in a namespace for either cpu or memory, you and other clients, must specify either requests or limits for that resource, for every new Pod you submit. If you don't, the control plane may reject admission for that Pod.

- For other resources: ResourceQuota works and will ignore pods in the namespace without setting a limit or request for that resource. It means that you can create a new pod without limit/request for ephemeral storage if the resource quota limits the ephemeral storage of this namespace.



## Difference Between MB (Megabytes) and Mi (Mebibytes)
The key difference between MB and Mi lies in their base of calculation:

- MB (Megabyte): Uses the decimal (base-10) system.
  - 1 MB = 1,000,000 bytes (10^6 bytes).
- Mi (Mebibyte): Uses the binary (base-2) system.
  - 1 Mi = 1,048,576 bytes (2^20 bytes).

**Which is Bigger?**
- 1 Mi (Mebibyte) is larger than 1 MB (Megabyte).
  - 2 Mi = 2×1,048,576=2,097,1522×1,048,576=2,097,152 bytes.
  - 2 MB = 2×1,000,000=2,000,000, 2×1,000,000=2,000,000 bytes.

**Thus, 2 Mi > 2 MB.**

### Clarification

| 1 KB = 1000 Bytes | 1 KiB = 1024 Bytes|
|-------------------|-------------------|
| 1 MB = 1000 KB    | 1 MiB = 1024 KB   |
| 1 GB = 1000 MB    | 1 GiB = 1024 MB   |



## How to Define CPU Metrics in Kubernetes
Kubernetes measures CPU resources using millicores (m or milliCPU). This allows fine-grained control over CPU allocation.

### CPU Metrics Units:
1. CPU (Cores):

    -  1 means 1 full CPU core.
    - 0.5 means half a CPU core.
2. Millicores:

    - 1000m = 1 CPU core.
    - 500m = 0.5 CPU core.

## Lab 01

If we define the limit and quota in the quota then it is mandatory to define the limit and Quota for the POD or Deployment else it will not let you create the POD or Deployment. 

```sh
# Create the Namespace. This is the pre-requisite
kubectl create namespace it-team

# Create the Resource Quota YAML file

vim quota.yml

# Paste the below mentioned code in quota.yml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: quota
  namespace: it-team
spec:
  hard:
    pods: "4"
    memory: "256Mi"
    cpu: "1000m"
```
**Explanation:**
1. **resources** section:

- **requests**: This is the minimum amount of 'CPU' and 'memory' that the container is guaranteed. The scheduler uses these values to decide which node to place the pod on.
  - **memory**: "256Mi" ensures that 256 MiB of memory is reserved for the container.
  - **cpu**: "500m" means the container will need 0.5 CPU core.
- **limits**: This is the maximum amount of CPU and memory that the container can use.
  - **memory**: "512Mi" limits the container to 512 MiB of memory.
  - **cpu**: "1" limits the container to 1 full CPU core.

## CPU Resource Requests vs Limits
- Requests: The minimum guaranteed CPU for the container. The scheduler uses this value to decide on which node to place the pod.
- Limits: The maximum CPU the container is allowed to use. If a container exceeds its limit, it may be throttled.



***
```sh
# Create the Resource Quota
kubectl apply -f quota.yml


# Create the Deployment and POD with Resource Quota
vim web-server.yml

# Paste the below mentioned code in the web-server.yml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: web-server
  name: web-server
  namespace: it-team
spec:
  replicas: 3
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
        resources:
          requests:
            memory: "256Mi"
            cpu: "1000m"
          limits:              # This is to define the Limit (Upper Limit)
            memory: "512Mi"
            cpu: "2000m"

# Create the PODs and Deployments
kubectl apply -f web-server.yml

# Describe Quota to check how much quota is used.

kubectl describe quota <Quota Name> -n it-team

```

