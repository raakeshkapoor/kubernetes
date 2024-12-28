# Install Kubernetes
Kubernetes (K8s) is a powerful open-source platform for managing containerized workloads and services. This guide will walk you through installing Kubernetes on a Debian server and setting up a two-node cluster for testing or development purposes. Let’s get started.

## Step-01: Configure Host-Name on both the Nodes
- ***Configure Host-Name on Control-Plane (Master)***
```sh
sudo hostnamectl set-hostname "master01"
```

- ***Configure Host-Name on Data-Plane (Worker)***
```sh
sudo hostnamectl set-hostname "worker01"
```

## Step-02: Configure /etc/hosts file on both Nodes
| IP Address | Hostname|
| :-------- | :------- |
| `192.168.1.10` | `master01` |
| `192.168.1.11` | `worker01` |

## Step-03: Update and Upgrade Packages on both Nodes

```sh
sudo apt update && apt upgrade -y
```

## Step-04: Disable SWAP on both Nodes
- Disable SWAP on all the Nodes i.e. on Master and Worker.
- Kubernetes requires swap to be disabled for consistent performance and resource management.
- If swap is enabled, Kubernetes nodes may not behave as expected because the kubelet (Kubernetes agent running on nodes) prioritizes real memory and does not handle swapping effectively.
```sh
sudo swapoff -a
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
```


## Step-05: Configure Configure Kernel Parameters and Install Container Runtime (Containerd) on both Nodes
- One of the pre-requisites of Kubernetes is to install the Container runtime and Containerd is the standard Container Runtime required to install.
```sh
cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf 
overlay 
br_netfilter
EOF
```
```sh
sudo modprobe overlay 
sudo modprobe br_netfilter
```
```sh
sudo cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1 
net.bridge.bridge-nf-call-ip6tables = 1 
EOF
```
```sh
sudo sysctl --system
```
```sh
sudo apt update
sudo apt -y install containerd
```
```sh
containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
```
```sh
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml
```
```sh
sudo systemctl restart containerd
sudo systemctl enable containerd
```

## Step-06: Add the Kubernetes repository to your Debian systems on both Nodes
```sh
sudo mkdir -p /etc/apt/keyrings

echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```

## Step-07:  Install Kubernetes Tools on both Nodes
```sh
sudo apt update
sudo apt install kubelet kubeadm kubectl -y
sudo apt-mark hold kubelet kubeadm kubectl
```

## Step-08: Set Up the Kubernetes Cluster with Kubeadm on the **Master Node**
```sh
sudo cat <<EOF | sudo tee kubelet.yaml
apiVersion: kubeadm.k8s.io/v1beta3
kind: InitConfiguration
---
apiVersion: kubeadm.k8s.io/v1beta3
kind: ClusterConfiguration
kubernetesVersion: "1.30.0"  # Replace with your desired version
---
apiVersion: kubelet.config.k8s.io/v1beta1
kind: KubeletConfiguration
EOF
```


## Step-09: Initialize the Kubernetes cluster on the **Master node**
```sh
sudo kubeadm init --config kubelet.yaml
```


## Step-10:  Setup the Kubectl on the Master Node for Regular User
```sh
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## Step-11:  Install Calico on the Master Node
```sh
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/calico.yaml
```

## Step-12:  Join Worker to Kubernetes, Run on the Worker Node
- Replace the Token and the CA details received as an output of **Step-09**
- Assuming 192.168.1.10 is the IP address of your Master Node, replace if the IP address of your Master node is different.
  
```sh
kubeadm join --token abcdef.1234567890abcdef --discovery-token-unsafe-skip-ca-verification 192.168.1.10:6443
```



