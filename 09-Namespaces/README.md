# Kubernetes Namespace

In Kubernetes, namespaces provide a mechanism for isolating groups of resources within a single cluster. Names of resources need to be unique within a namespace, but not across namespaces. Namespace-based scoping is applicable only for namespaced objects (e.g. Deployments, Services, etc.) and not for cluster-wide objects (e.g. StorageClass, Nodes, PersistentVolumes, etc.).

**Note: **

By default all the PODS get created in the default Namespace.

## When to Use Multiple Namespaces

Namespaces are intended for use in environments with many users spread across multiple teams, or projects. For clusters with a few to tens of users, you should not need to create or think about namespaces at all. Start using namespaces when you need the features they provide.

Namespaces are a way to divide cluster resources between multiple users (via resource quota).

It is not necessary to use multiple namespaces to separate slightly different resources, such as different versions of the same software: use labels to distinguish resources within the same namespace.

**Note:**

For a production cluster, consider not using the default namespace. Instead, make other namespaces and use those.

## Namespaces and DNS

When you create a Service, it creates a corresponding DNS entry. This entry is of the form **<service-name>**.`<namespace-name>.svc.cluster.local`, which means that if a container only uses `<service-name>`, it will resolve to the service which is local to a namespace. This is useful for using the same configuration across multiple namespaces such as Development, Staging and Production. 

If you want to reach across namespaces, you need to use the fully qualified domain name (FQDN).

```sh
# View Namespaces
kubectl get namespace

# Get PODs from a particular Namespace. It will show all the PODs running in kube-system Namespace.
kubectl get pods -n kube-system

# Command to creat a new Namespace. Namespace name needs to be in lower-case.

kubectl create namespace it-team

# Verify new Namespace
kubectl get namespace

# Create a POD in the it-team Namespace by using either of these commands. 
kubectl run web-server --image=nginx -n it-team

kubectl run web-server --image=nginx --namespace=it-team

# Verify the POD Created by using either of these commands.
kubectl get pods -n it-team
kubectl get pods --namespace=it-team

# Delete POD
kubectl delete pod web-server2 -n it-team
kubectl delete pod web-server --namespace=it-team

# Delete Namespace
kubectl delete namespace it-team

```

## Create POD with the same name in two different Namespaces. 

## Create a Deployment in the Namespace

```sh
# Make sure Namespace is created before creating the Deployment
kubectl create deployment web-server --image=nginx -n it-team

# Verify deployment
kubectl get all -n it-team

# Delete deployment
kubectl delete deployment web-server -n it-team
```

## Create a Deployment in it-team Namespace by defining in YAML file

```sh
# Make sure Namespace is already created before creating the deployment. 

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

# Create deployment
kubectl apply -f web-server.yml

# Verify Deployments
kubectl get deployment web-server -n it-team

# Verify PODs
kubectl get pods -n it-team

# Delete the deployment
kubectl delete deployment web-server -n it-team

```

## Create a Deployment in it-team Namespace without defining in the YAML file

```sh
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: web-server
  name: web-server
#  namespace: it-team
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

# Create deployment
kubectl apply -f web-server.yml -n it-team

# Verify Deployments
kubectl get deployment web-server -n it-team

# Verify PODs
kubectl get pods -n it-team

# Delete the deployment
kubectl delete deployment web-server -n it-team
```

# Change the Default Namespace

```sh
# Get Context
kubectl config get-contexts

# Output is given-below, '*' represents the default Context. 
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   


# Creat Context
kubectl config set-context it-team --namespace=it-team --user=kubernetes-admin --cluster=kubernetes

# Verify the new Context created
kubectl config get-contexts

# Switch the context to "it-team".
kubectl config use-context it-team

# Verify the default Context
kubectl config get-contexts

# Now verify the PODs in "it-team" Namespace
kubectl get pods -n it-team

# Create new POD without defining the Namespace. This would create the POD in "it-team" Namespace.
kubectl run web-server --image=nginx

# Verify the POD
kubectl get pods -n default
kubectl get pods -n it-team

# As the defult config is changed, command without the namespace will show the POD
kubectl get pods

```

## Switch to the Default Config

```sh
kubectl configure get-contexts

kubectl config use-context kubernetes-admin@kubernetes

# Delete context
kubectl config delete-context it-team

# Verify
kubectl config get-contexts

# Verify by creating PODs
kubectl run web-server --image=nginx

```
