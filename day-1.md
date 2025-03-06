# What and Why K8s

- Container is a great way to package and run application, but in production, we need high availability (HA) and scalability. If one container fails, another should start automatically.  
- Kubernetes (K8) is an open-source container orchestration tool that automates application deployment, ensures HA, and scales application.  

**Key Features of K8**  

- `Service Discovery & Load Balancing` → Container is ephemeral, so relying on IP is not possible. K8 provides Service to handle communication and load balancing.  
- `Storage Management` → Automatically mounts volume to container.  
- `Rolling Updates & Rollbacks` → Automates releases, and if something goes wrong, you can rollback to the previous version.  
- `Self-Healing` → If a container fails, K8 will automatically restart it.  
- `Configuration & Secret Management` → Manages config and secret externally, avoiding hardcoding inside container.  
- `IPv4 & IPv6 Support` → Supports both IP versions for networking.  
- `Extend K8 Functionality` → Use Custom Resource Definition (CRD) to add extra features.  
- `Resource Limits` → Define CPU and RAM limits for container to optimize resource usage.  

# K8s Architecture

Kubernetes (K8s) Architecture  

A K8s Cluster consists of Control Plane Nodes and Worker Nodes.  

1. `Control Plane (Manages the Cluster)`
The control plane is responsible for the complete orchestration and management of workloads.  

`Key Components & Activities:`  
- `API Server (kube-apiserver)` → Acts as the entry point for all K8s operations, handling requests from users, CLI, and internal components.  
- `Scheduler (kube-scheduler)` → Assigns workloads (Pods) to suitable worker nodes based on resource availability.  
- `Controller Manager (kube-controller-manager)` → Runs different controllers to maintain cluster state (e.g., ensuring desired Pods are running).  
- `ETCD` → A distributed key-value store that stores all cluster configurations, states, and metadata.  
- `Cloud Controller Manager (optional)` → Manages cloud-specific integrations like load balancers and networking.  

2. `Worker Node (Runs Applications & Workloads)`  
Worker nodes host and execute containerized applications.  

`Key Components & Activities:`  
- `Kubelet` → Communicates with the control plane, ensuring Pods are running as expected.  
- `Container` Runtime (Docker, containerd, CRI-O, etc.) → Runs and manages containerized workloads.  
- `Kube Proxy` → Manages networking and load balancing between Pods and Services.  
- `Pods` → The smallest deployable unit in Kubernetes that contains one or more containers.  

# Pod

### What is a Pod?  
A Pod is the smallest deployable unit in Kubernetes. It represents a logical host for one or more containers that share the same network and storage.  

### Pod Lifecycle  
A Pod goes through different phases from creation to termination. The Pod Lifecycle Phases are:  

1. Pending → The Pod is created but not yet scheduled on a Node.  
2. Running → The Pod is scheduled on a Node, and all containers are running or starting.  
3. Succeeded → All containers in the Pod have completed successfully and will not restart.  
4. Failed → One or more containers in the Pod have failed, and Kubernetes will not restart them.  
5. Unknown → The Pod state cannot be determined due to communication failure with the Node.  

### Difference Between Container and Pod  

Feature | Container | Pod  
--- | --- | ---  
Definition | A lightweight, standalone executable unit that includes the application and dependencies. | A Kubernetes object that manages one or more containers as a unit.  
Networking | Has its own isolated network namespace. | Shares a network namespace with other containers inside the Pod.  
Storage | Uses ephemeral storage by default. | Can attach persistent storage and share it among containers.  
Scaling | Cannot be directly scaled in Kubernetes. | Scales as a whole unit in Kubernetes.  
Management | Managed by container runtimes (Docker, containerd). | Managed by Kubernetes.  

### InitContainers  
InitContainers are special containers that run before the main application containers in a Pod.  

### Why Use InitContainers?  
- To prepare the environment before the main application starts.  
- To ensure dependencies are met (e.g., waiting for a database to be ready).  
- To perform setup tasks like downloading configuration files.  

### Key Points  
- Runs sequentially before the main container starts.  
- Can have different images from the main container.  
- If an InitContainer fails, Kubernetes retries it until it succeeds or the Pod fails. 

### **Pod Management Commands in Kubernetes**  

#### **1. Create a Pod**  
```bash
kubectl run mypod --image=nginx
```  

#### **2. List All Pods**  
```bash
kubectl get pods
```  

#### **3. Get Pods in a Specific Namespace**  
```bash
kubectl get pods -n <namespace-name>
```  

#### **4. Get Detailed Pod Information**  
```bash
kubectl describe pod mypod
```  

#### **5. Get Pod Logs**  
```bash
kubectl logs mypod
```  

#### **6. Stream Logs in Real Time**  
```bash
kubectl logs -f mypod
```  

#### **7. Execute a Command Inside a Running Pod**  
```bash
kubectl exec mypod -- ls /app
```  

#### **8. Run an Interactive Shell Inside a Pod**  
```bash
kubectl exec -it mypod -- /bin/bash
```  

#### **9. Get Pods with Wide Output (More Details)**  
```bash
kubectl get pods -o wide
```  

#### **10. Delete a Pod**  
```bash
kubectl delete pod mypod
```  

#### **11. Delete All Pods in a Namespace**  
```bash
kubectl delete pods --all -n <namespace-name>
```  

#### **12. Restart a Pod (Delete and Recreate in a Deployment/ReplicaSet)**  
```bash
kubectl rollout restart deployment mydeployment
```  

#### **13. Get Pod Events and Reasons for Failures**  
```bash
kubectl get events --sort-by=.metadata.creationTimestamp
```  

#### **14. Get Pod Resource Usage (CPU & Memory)**  
```bash
kubectl top pod
```  

#### **15. Get Pod's YAML Definition**  
```bash
kubectl get pod mypod -o yaml
```  

#### **16. Create a Pod from a YAML File**  
```bash
kubectl apply -f mypod.yaml
```  

#### **17. Delete a Pod from a YAML File**  
```bash
kubectl delete -f mypod.yaml
```  

### What is a Pause Container?  
A Pause Container is a special container that acts as the parent container for all containers within a Pod in Kubernetes. It is created automatically by Kubernetes when a Pod is scheduled.  

### Why is a Pause Container Needed?  
Kubernetes uses Pause Containers for networking and process management within a Pod.  

1. Pod’s Network Namespace →  
   - All containers in a Pod share the same network namespace.  
   - The Pause Container holds this namespace, allowing other containers to join it.  
   - This ensures containers in a Pod can communicate internally using `localhost`.  

2. Pod Lifecycle Management →  
   - Even if application containers restart, the Pause Container keeps the Pod’s IP and network consistent.  
   - Without it, network settings would reset every time a container restarts.  

### How Does it Work?  
- When a Pod is created, Kubernetes first starts a Pause Container.  
- All other containers in the Pod use the network and process namespaces of the Pause Container.  
- The Pause Container does nothing except sleep indefinitely to keep the namespace alive.  

### Command to View Pause Containers  
On a Kubernetes Node, you can check the running Pause Containers using:  
```bash
docker ps | grep pause  
# OR for containerd
crictl ps | grep pause
```
### Key Takeaways  
- The Pause Container is the first container started in a Pod.  
- It acts as a namespace holder for networking and process sharing.  
- It ensures that the Pod’s IP and network remain stable, even if application containers restart.  
- It uses very low resources since it only runs a sleep process.  

# NameSpaces
### What is a Namespace in Kubernetes?  
A **Namespace** in Kubernetes is a way to logically isolate resources within a cluster. It allows multiple teams or applications to share a single cluster while keeping their resources separate.  

### Use Cases of Namespaces  
1. Multi-Tenancy → Different teams or projects can use separate namespaces to avoid resource conflicts.  
2. Resource Isolation → Ensures that different applications do not interfere with each other.  
3. Access Control → Role-Based Access Control (RBAC) can be applied at the namespace level for security.  
4. Resource Quotas → Helps in limiting CPU, memory, and storage usage per namespace.  
5. Organized Management → Allows grouping related resources, making it easier to manage complex clusters.  

### Common Namespace Commands  

1. View Existing Namespaces  
```bash
kubectl get namespaces  
```  

2. Create a New Namespace  
```bash
kubectl create namespace <namespace-name>
```  

3. Delete a Namespace  
```bash
kubectl delete namespace <namespace-name>
```  

4. View Resources in a Specific Namespace  
```bash
kubectl get pods -n <namespace-name>
kubectl get services -n <namespace-name>
```  

5. Set a Default Namespace for kubectl  
```bash
kubectl config set-context --current --namespace=<namespace-name>
```  

6. Run a Pod in a Specific Namespace  
```bash
kubectl run nginx --image=nginx -n <namespace-name>
```  

### Default Namespaces in Kubernetes  
- default → Used when no namespace is specified.  
- kube-system → Contains system-related components like the API server and controller manager.  
- kube-public → Accessible to all users; used for public information.  
- kube-node-lease → Manages node heartbeats to determine availability.  

# Labels & Selectors
### Labels & Selectors in Kubernetes  

#### Labels Purpose  
Labels are key-value pairs attached to Kubernetes objects (Pods, Services, Deployments, etc.). They help in organizing and grouping resources for easier management.  

#### Why Use Labels?  
- Grouping Resources → Identify and group related resources (e.g., all Pods of an app).  
- Selection & Filtering → Easily find resources using `kubectl` commands.  
- Scaling & Updates → Controllers (like Deployments) use labels to manage Pods dynamically.  
- Network Policies → Used to define security rules based on labels.  

#### Selectors Purpose  
Selectors help to filter and match Kubernetes objects based on their labels. They are mainly used by Services, Deployments, and other controllers to target specific Pods.  

### Types of Label Selectors  

#### 1. Equality-Based Selectors  
These selectors match objects where the label value is exactly equal (`=` or `==`) or not equal (`!=`).  

Examples:  
- Get Pods where the label **app=nginx**:  
  ```bash
  kubectl get pods -l app=nginx
  ```
- Get Pods where the label **tier is not equal to frontend**:  
  ```bash
  kubectl get pods -l tier!=frontend
  ```

#### 2. Set-Based Selectors  
These selectors match objects where the label value is in a set or not in a set.  

Examples:  
- Get Pods where **env is either dev or test**:  
  ```bash
  kubectl get pods -l 'env in (dev,test)'
  ```
- Get Pods where **env is not in staging or prod**:  
  ```bash
  kubectl get pods -l 'env notin (staging,prod)'
  ```
- Get Pods that have the label **app** (regardless of value):  
  ```bash
  kubectl get pods -l app
  ```

### Common Commands for Labels & Selectors  

#### 1. Add a Label to a Resource  
```bash
kubectl label pod mypod app=nginx
```  

#### 2. Remove a Label from a Resource  
```bash
kubectl label pod mypod app-
```  

#### 3. View Labels for All Pods  
```bash
kubectl get pods --show-labels
```  

#### 4. Get Pods Matching a Label Selector  
```bash
kubectl get pods -l app=nginx
```  

#### 5. Get Resources with Multiple Label Conditions  
```bash
kubectl get pods -l app=nginx,env=dev
```  
