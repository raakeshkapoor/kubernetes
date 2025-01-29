# Map Azure Files with Kubernetes POD

https://github.com/kubernetes-sigs/azurefile-csi-driver/blob/master/docs/install-csi-driver-v1.31.0.md

## 1.  Create the Azure Secret

```sh
# Create the Azure Secret file and paste the code given below:
kubectl create secret generic azure-secret \
  --from-literal=azurestorageaccountname=<Storage_Account_Name> \
  --from-literal=azurestorageaccountkey=<Storage_Account_Key>
```

## 2. Create the PV File

```sh
# Create the pv.yml file
vim pv.yml

# Paste the below-mentioned code
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-volume
spec:
  capacity:
    storage: 5Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  storageClassName: slow
  csi:
    driver: file.csi.azure.com
    volumeHandle: "k8sazurefiles001#salesdata"
    volumeAttributes:
      shareName: salesdata
    readOnly: false
    nodeStageSecretRef:
      name: azure-secret
      namespace: default

```

## 3. Create the PVC File

```sh
# Create the pvc.yml file
vim pvc.yml

# Paste the below-mentioned code

apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-volume
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 5Gi
  storageClassName: slow

```

## 4. Create the POD File

```sh
# Create the pod.yml file
vim pod.yml

# Paste the below mentioned code

apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - name: nginx
      image: nginx
      volumeMounts:
        - mountPath: "/mnt/azure"
          name: my-volume
  volumes:
    - name: my-volume
      persistentVolumeClaim:
        claimName: my-volume

```

## 5. Install the azurefile CSI driver v1.31.0 version on a Kubernetes cluster

```sh
# Install by Kubectl 

curl -skSL https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/v1.31.0/deploy/install-driver.sh | bash -s v1.31.0 --

# Check POD status:

kubectl -n kube-system get pod -o wide --watch -l app=csi-azurefile-controller
kubectl -n kube-system get pod -o wide --watch -l app=csi-azurefile-node

# Example Output
NAME                                            READY   STATUS    RESTARTS   AGE     IP             NODE
csi-azurefile-controller-56bfddd689-dh5tk       6/6     Running   0          35s     10.240.0.19    k8s-agentpool-22533604-0
csi-azurefile-node-cvgbs                        3/3     Running   0          7m4s    10.240.0.35    k8s-agentpool-22533604-1
csi-azurefile-node-dr4s4                        3/3     Running   0          7m4s    10.240.0.4     k8s-agentpool-22533604-0

# Uninstall Driver if required:
curl -skSL https://raw.githubusercontent.com/kubernetes-sigs/azurefile-csi-driver/v1.31.0/deploy/uninstall-driver.sh | bash -s --

```
## 6. Run the files

```sh
kubectl apply -f pv.yml

kubectl apply -f pvc.yml

kubectl apply -f pod.yml

```
## 7. Login to POD and Create a File

```sh
# Login to POD
kubectl exec -it nginx -- /bin/bash

# Change directory to the Mount directory
cd /mnt/azure

# Create a file
echo "Welcome to my POD" > test-file.txt

# Verify the content of the file
cat test-file.txt

# Once the file is created, browse to the Azure Files to check if the file is showing in Azure File or not. It will show in the Azure Portal under the Storage Account Created. 


```
