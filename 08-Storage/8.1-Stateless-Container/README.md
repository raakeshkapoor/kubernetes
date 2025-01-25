# Create a POD with local Stateless Storage

```sh
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - name: nginx-vol
      mountPath: /tmp/volume
  volumes:
  - name: nginx-vol
    emptyDir: {}

```
**NOTE:**

- The name in `volumes` defines the volume resource at the pod level.
- The name in `volumeMounts` tells Kubernetes which volume to mount into the container.
- The name of `volumes` and `volumeMounts` need to match. 

## **Sequence for handling volumes and mounting them into containers works in the following order:**

**1. Volume is Created at the Pod Level**

- When a Pod is scheduled to a node, Kubernetes first sets up all the `volumes` defined in the volumes section of the Pod specification.
- These `volumes` are created before the `containers` within the Pod are started.
- Depending on the volume type, this may involve:
    - Setting up a directory for emptyDir.
    - Attaching a Persistent Volume (e.g., an Azure Disk or AWS EBS volume) to the node.
    - Preparing network-based volumes like NFS or CSI drivers.

**2. Containers are Started**

- Once the volumes are ready, Kubernetes starts the containers defined in the Pod.
- During container initialization, Kubernetes mounts the volumes to the specified paths inside the container(s) as defined in the `volumeMounts` section.
- This ensures the volume is available at the designated mount points as soon as the container starts.

**Sequence for Clarity**

1. Pod creation initiated.
2. Kubernetes processes the `volumes` section:
   - Creates or initializes the volume resource.
3. Kubernetes starts the container(s):
   - For each container:
     - Mounts the volumes (from `volumeMounts`) to the specified paths.
     - Starts the container.
4. Containers are running, and `volumes` are accessible inside them.

**Run the describe command to check the Mounts**

```sh
kubectl describe pod nginx
```

**Login to the POD to check the Volume**

```sh
kubectl exec -it nginx -- /bin/bash

# Once logged in go to the path /tmp/volume
cd /tmp/volume

# Create a file in this path, delete and recreate the POD. It will delete the file created.
echo "Welcome to my POD" > test-file.txt
cat test-file.txt

```
