# Pre-requisite
Before diving into the installation, ensure the following:
- A Debian server with at least 2 CPUs, 2GB of RAM, and 20GB of disk space.
- A non-root user with sudo privileges.
- Basic familiarity with the command line.


To install the Kubernetes in the test lab, we need to have at least 2 Servers. 
We'll use Debian to configure Control-Plane (Mater) and Data-Plane(Worker).
Install Debian on 2 Servers and assign the Name and IP Address as mentioned below:

## Node01: Control-Plane (Mater) Configuration
**Host Name**: Master01

**IP Address**: 192.168.1.10

**Firewall**: Disable  

**Internet**: Enable

## Node02: Data-Plane (Worker) Configuration
**Host Name**: Worker01

**IP Address**: 192.168.1.11

**Firewall**: Disable  
  Command to disable Firewall: **sudo ufw disable**
  
**Internet**: Enable

## Step 01: Configure the following on both the Nodes
```
sudo apt update -y
sudo apt install curl ufw vim -y
```
## Step 02: Disable Firewall on both the Nodes
In the production environment, it is not recommended to disable Firewall. It is only for the Lab purpose. In the Production environment, it is recommended to add the rules to allow the required ports on both Master and Worker. 
Command to disable Firewall - 
```
sudo ufw disable
```
