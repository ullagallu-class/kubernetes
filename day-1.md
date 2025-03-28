# What and Why K8s

Containers are a good way to bundle and run your applications. In a production environment, you need to manage the containers that run the applications and ensure that there is no downtime. For example, if a container goes down, another container needs to start.

Kubernetes provides you with a framework to run distributed systems resiliently. It takes care of scaling and failover for your application, provides deployment patterns

In Kubernetes, **resiliently** means that your application can handle failures and disruptions without significant downtime or manual intervention. Kubernetes ensures resilience by:  

1. **Self-healing** – If a pod crashes, Kubernetes automatically restarts it. If a node fails, pods are rescheduled to a healthy node.  
2. **Scaling** – It dynamically scales up or down based on load, ensuring the system adapts to demand.  
3. **Load balancing** – Traffic is distributed among healthy pods, preventing overload on a single instance.  
4. **Rolling updates & rollbacks** – It deploys updates gradually and can revert if an issue occurs.  
5. **Fault tolerance** – With multi-node clusters, failures in one node don’t impact the entire application.  

Essentially, **Kubernetes ensures that your system remains operational and performs well, even when failures occur.**

Kubernetes (K8) is an open-source container orchestration tool that automates application deployment, ensures HA, and scales application.  

**Key Features of K8**  

- `Service Discovery & Load Balancing` → Container is ephemeral, so relying on IP is not possible. K8 provides Service to handle communication and load balancing.  
- `Storage Management` → Automatically mounts volume to container.  
- `Rolling Updates & Rollbacks` → Automates releases, and if something goes wrong, you can rollback to the previous version.  
- `Self-Healing` → If a container fails, K8 will automatically restart it.  
- `Configuration & Secret Management` → Manages config and secret externally, avoiding hardcoding inside container.  
- `IPv4 & IPv6 Support` → Supports both IP versions for networking.  
- `Extend K8 Functionality` → Use Custom Resource Definition (CRD) to add extra features.  
- `Resource Limits` → Define CPU and RAM limits for container to optimize resource usage.  
---
# K8s Architecture

Kubernetes (K8s) is a distributed architecture by default. It is designed to manage containerized applications across multiple nodes in a cluster, ensuring high availability, scalability, and resilience.

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
- `kubectl (Kubernetes command-line tool)` is used to interact with Kubernetes clusters. It allows you to deploy applications, inspect and manage cluster resources, and troubleshoot issues in a Kubernetes environment.
---
- kubeadm cluster creation
---
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

7. Get the current namespace
```bash
kubectl config view --minify
```
8. Get the all contexts avialable in your cluster
```bash
kubectl config get-contexts
```
9. Switch to another context:
```bash
kubectl config use-context test-context
```
### Default Namespaces in Kubernetes  
- default → Used when no namespace is specified.  
- kube-system → Contains system-related components like the API server and controller manager.  
- kube-public → Accessible to all users; used for public information.  
- kube-node-lease → Manages node heartbeats to determine availability.  
---
# Pod
### What is a Pod?  
A Pod is the smallest deployable unit in Kubernetes. It acts as a logical host for one or more containers that share the same network namespace, storage, and lifecycle.  

A Pod has a single IP, regardless of the number of containers inside it.  
- All containers within a pod share the same network namespace, allowing them to communicate using localhost.  
- The best practice is to have one container per pod unless additional sidecar containers (e.g., for logging or monitoring) are needed.  

Pods are ephemeral (short-lived).  
- Kubernetes does not guarantee pod longevity. If a node fails or a pod is deleted, it won’t automatically restart unless managed by a controller such as a Deployment, StatefulSet, or DaemonSet.  
- Applications should be designed with fault tolerance to handle pod failures effectively.  

Avoid running standalone pods without controllers.  
- Standalone pods do not have a mechanism to restart automatically if they fail.  
- To ensure high availability and self-healing, use controllers such as:  
  - Deployments for stateless applications  
  - StatefulSets for stateful applications  
  - DaemonSets for running pods on every node  
  - Job/CronJob for batch and scheduled tasks

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

#### **12. Get Pod Resource Usage (CPU & Memory)** **need to install metric server** 
```bash
kubectl top pod
```  

#### **13. Get Pod's YAML Definition**  
```bash
kubectl get pod mypod -o yaml
```  

#### **14. Create a Pod from a YAML File**  
```bash
kubectl apply -f mypod.yaml
```  

#### **15. Delete a Pod from a YAML File**  
```bash
kubectl delete -f mypod.yaml
``` 
### **16. Creating yaml definition from kubectl command**
```bash
kubectl run mypod --image=nginx --dry-run=client -o yaml > mypod.yaml
```

### Pod Lifecycle  
A Pod goes through different phases from creation to termination. The Pod Lifecycle Phases are:  

1. Pending → The Pod is created but not yet scheduled on a Node.  
2. Running → The Pod is scheduled on a Node, and all containers are running or starting.  
3. Succeeded → All containers in the Pod have completed successfully and will not restart.  
4. Failed → One or more containers in the Pod have failed, and Kubernetes will not restart them.  
5. Unknown → The Pod state cannot be determined due to communication failure with the Node.

When we say **"Pod is created"**, it means the **Kubernetes API server** has received the request to create a Pod and has stored its definition in **etcd (Kubernetes' key-value store)**.  

At this point, the Pod object **exists in Kubernetes**, but it does not yet have a Node assigned to run on. The Kubernetes scheduler is responsible for picking a suitable Node based on resources, affinity rules, and other constraints.  

So, **"Pod created" does not mean it is running**—it only means that Kubernetes knows about it and is working on placing it on a Node.

### Difference Between Container and Pod  

Feature | Container | Pod  
--- | --- | ---  
Definition | A lightweight, standalone executable unit that includes the application and dependencies. | A Kubernetes object that manages one or more containers as a unit.  
Networking | Has its own isolated network namespace. | Shares a network namespace with other containers inside the Pod.  
Storage | Uses ephemeral storage by default. | Can attach persistent storage and share it among containers.  
Scaling | Cannot be directly scaled in Kubernetes. | Scales as a whole unit in Kubernetes.  
Management | Managed by container runtimes (Docker, containerd). | Managed by Kubernetes.  

Example of Init Containers in Kubernetes  

Init containers are specialized containers that run before the main application container starts. They are used for tasks like setting up configurations, waiting for dependencies, or performing database migrations.  

Scenario:  
You have an Nginx pod, but before it starts, you need to download an index.html file from an external server and place it in a shared volume.  

YAML Example:  
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
   app: nginx
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - name: shared-data
      mountPath: /usr/share/nginx/html
  initContainers:
  - name: init-git-clone
    image: alpine/git
    command:
    - sh
    - "-c"
    - |
      git clone https://github.com/sivaramakrishna-konka/hello-world.git /work-dir && \
      cp /work-dir/wish.html /work-dir/index.html
    volumeMounts:
    - name: shared-data
      mountPath: /work-dir
  volumes:
  - name: shared-data
    emptyDir: {}
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```  

How It Works:  
1. The `init-downloader` container runs first. It:  
   - Uses the `busybox` image.  
   - Runs `wget` to download an HTML file.  
   - Stores the file in a shared volume (`emptyDir`).  

2. Once the init container completes, the main Nginx container starts.  
   - It serves the downloaded `index.html` from the shared volume.  

Key Points About Init Containers:  
- They always run before the main container.  
- They must complete successfully before the main container starts.  
- They can have different images and dependencies from the main container.  
- All init containers are run sequentially, meaning one must complete before the next starts.  
- Useful for setup tasks, data preparation, and waiting for services.
---
# Shared Netwrok
Yes, if two containers are in the **same Pod**, they share the **same network namespace**, meaning they can communicate with each other using `localhost`.  

### **Example: Shared Network in a Pod**  
Here’s a simple example where two containers (`nginx` and `alpine`) are in the same Pod, and the `alpine` container pings the `nginx` container using `localhost`.  

#### **YAML Example:**  
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: shared-network-pod
spec:
  containers:
  - name: nginx
    image: nginx
  - name: alpine
    image: alpine
    command: ["sh", "-c", "sleep 3600"]
```

#### **How It Works:**  
1. The Pod has **two containers**:  
   - `nginx` (web server)  
   - `alpine` (used for testing network communication)  

2. Since they are in the **same Pod**, they share the same **network namespace** and can communicate over `localhost`.  

#### **Testing Inside the Pod:**  
After the Pod is running, you can **exec into the `alpine` container** and ping the `nginx` container:  
```sh
kubectl exec -it shared-network-pod -c alpine -- sh
```
Now inside the container, try:  
```sh
ping localhost
curl localhost
```
Both should work, confirming that both containers share the same network namespace.

#### **Key Points:**  
- Containers in the same **Pod** can communicate over `localhost`.  
- They **do not** need a separate IP address since they share the same **network namespace**.  
- This is useful for **sidecar patterns**, such as logging, monitoring, or security proxies.  
---
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
sudo ctr -n k8s.io containers ls
sudo ctr -n k8s.io containers list
```
### Key Takeaways  
- The Pause Container is the first container started in a Pod.  
- It acts as a namespace holder for networking and process sharing.  
- It ensures that the Pod’s IP and network remain stable, even if application containers restart.  
- It uses very low resources since it only runs a sleep process.  

# Labels & Selectors
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
---
Yes! A **Kubernetes Cluster** consists of **Control Plane Nodes** and **Worker Nodes**.  

### **Control Plane Responsibilities**  
The **Control Plane** is responsible for decision-making in the cluster. It consists of multiple components that handle different tasks:

1. **Authentication & Authorization**  
   - The **API Server (kube-apiserver)** manages authentication and authorization.
   - It verifies if users, services, or components have the right permissions to access resources.

2. **Manifest File Validation**  
   - The API Server validates incoming Kubernetes YAML manifests before applying them.

3. **Scheduling**  
   - The **kube-scheduler** decides which worker node should run a Pod based on resource availability, constraints, and affinity rules.

4. **Storing Cluster Information**  
   - **etcd** (a distributed key-value store) stores all cluster data, including configuration, state, and resource information.

5. **Controller for HA, Scalability, and Self-Healing**  
   - The **kube-controller-manager** runs controllers that ensure high availability and scalability:
     - **Node Controller**: Detects failed nodes and reschedules Pods.
     - **Replication Controller**: Ensures the desired number of Pods are always running.
     - **Endpoint Controller**: Updates Service and Pod mappings.
     - **Job Controller**: Manages batch jobs and cleanup.
     - **Horizontal Pod Autoscaler (HPA)**: Scales Pods based on CPU/memory usage.

---

### **Worker Node Responsibilities**  
Worker Nodes **execute workloads** and run application containers.

| **Component**  | **Responsibility** |
|---------------|----------------|
| **Kubelet** | Ensures containers in Pods are running and healthy. |
| **Container Runtime** | Runs containers (Docker, containerd, CRI-O). |
| **Kube Proxy** | Handles networking, service discovery, and load balancing. |

---

### **Summary**
- The **Control Plane** is the brain of Kubernetes, making all the decisions.
- The **Worker Nodes** execute those decisions and run application workloads.

