# Persistent Volume

## Create a NFS Share

Create NFS Share on the Debian Server.

```sh
sudo apt-get update

# Install NFS Server Package
sudo apt install nfs-kernel-server -y

# Install the VIM package
sudo apt install vim -y

# Start the NFS service and enable it to start on boot
sudo systemctl start nfs-kernel-server
sudo systemctl enable nfs-kernel-server

# Verify the staus of NFS service
sudo systemctl status nfs-kernel-server

# Make a directory that will be shared as a NFS Share.
sudo mkdir -p /mnt/nfsshare

# NFS share will be used by any user on the client
sudo chown nobody:nogroup /mnt/nfsshare

# Assign the read and write permission on the directory so that user on client machine.
sudo chmod 755 /mnt/nfsshare

# Update the export information in /etc/exports file
sudo vim /etc/exports

/mnt/nfsshare 192.168.1.0/24(rw,sync,no_subtree_check)

# Export the Shared Directory
sudo exportfs -a

# Disable Firewall
# Either use this command to add the Firewall rule if you don't want to disable Firewall. sudo ufw allow from 192.168.1.0/24 to any port nfs
sudo apt install ufw -y
sudo service ufw disable

# Check Status of the Firewall
sudo service ufw status

# On the Client Machines i.e. the Kubernetes Workers run the following command to enable the NFS client
sudo apt install nfs-common -y

```

## Create YAML Files for PV, PVC and POD

```sh
# Create the PV file

vim pv.yml

# Copy and paste the below mentioned code to the YAML file. 

apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv001
spec:
  capacity:
    storage: 5Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: slow
  nfs:
    path: /mnt/nfsshare           # Path of NFS to mount
    server: 192.168.1.170         # IP Address of the NFS Server

# Create the PVC File and paste the below mentioned code

vim pvc.yml

# Paste the below mentioned Code in the pvc.yml file. 
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: volclaim
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 1Gi
  storageClassName: slow

# Create the POD YAML file and save the below mentioned code in the pod.yml file
vim pod.yml

# Paste the below mentioned code in the pod.yml file

apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - name: my-container
      image: nginx:latest
      volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: my-volume
  volumes:
    - name: my-volume
      persistentVolumeClaim:
        claimName: volclaim


```

