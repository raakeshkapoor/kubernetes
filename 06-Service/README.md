# Kubernetes Services
In Kubernetes, a Service is a method for exposing a network application that is running as one or more Pods in your cluster.

**ClusterIP, NodePort, and LoadBalancer** are different types of services that determine how the application inside a Kubernetes cluster is accessed

## Type of Services

**1. ClusterIP:**

- **Default Service Type:** This is the default service type in Kubernetes.
- **Internal Access Only:** The service is exposed only inside the Kubernetes cluster. It can't be accessed from outside the cluster.
- **Use Case:** It is typically used for internal communication between Pods or services within the same cluster.
- **Access:** You can access the service using its IP from within the cluster.

**2. NodePort:**

- **External Access:** A NodePort service exposes the service on a specific port of each Node in the cluster.
- **Access from Outside the Cluster:** By using any Node’s IP address and the specified port, you can access the service externally (outside the cluster).
- **Port Range:** The port is chosen from a predefined range (typically 30000–32767).
- **Use Case:** It's useful for debugging or when you want to expose a service externally, but don't need a full-fledged load balancer.
- **Access:** ***NodeIP:NodePort*** can be used to access the service externally.

**3. LoadBalancer:**

- **External Access with Load Balancing:** The LoadBalancer service type provisions an external load balancer that routes traffic to the appropriate pods within the cluster.
- **Cloud Provider Integration:** This type typically integrates with cloud providers (*like AWS, GCP, Azure*) to provision a cloud load balancer.
- **Automatic External IP Assignment:** The service is assigned an external IP address (from the cloud provider) that routes traffic to the Pods.
- **Use Case:** Ideal for production-grade applications where you need robust external access with automatic load balancing.
- **Access:** The service can be accessed via a single external IP address provided by the load balancer.
