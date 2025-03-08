### **ReplicaSet & ReplicationController in Kubernetes**  

#### **1. What is a ReplicationController?**  
A **ReplicationController** ensures that a specified number of Pod replicas are running at all times. If a Pod fails or is deleted, it automatically creates a new Pod to maintain the desired state.  

##### **Key Features:**  
- Ensures a fixed number of Pod replicas.  
- Replaces failed or terminated Pods.  

##### **Limitations:**  
- Does not support advanced selectors.  
- Considered **deprecated** in favor of **ReplicaSet**.  

---

#### **2. What is a ReplicaSet?**  
A **ReplicaSet** is the next-generation version of ReplicationController with more flexible **label selectors**. It ensures that a specified number of Pods are always running and automatically replaces failed Pods.  

##### **Key Features:**  
- Supports **set-based selectors** (e.g., `env in (dev, prod)`).  
- Ensures high availability by maintaining the desired number of replicas.  
- Works with **Deployments** for rolling updates and rollback features.  
---
### **Comparison: ReplicaSet vs. ReplicationController**  

| Feature               | ReplicationController | ReplicaSet |
|-----------------------|----------------------|-----------|
| Ensures replica count | ✅ Yes | ✅ Yes |
| Auto-replaces failed Pods | ✅ Yes | ✅ Yes |
| Supports rolling updates | ❌ No | ✅ Yes (with Deployments) |
| Supports set-based selectors | ❌ No | ✅ Yes |
| Considered deprecated | ✅ Yes | ❌ No |

#### **Which One to Use?**  
- **Use ReplicaSet** instead of ReplicationController since it is more advanced and supports flexible label selectors.  
- However, **Deployments** are preferred over ReplicaSets for better management, updates, and rollback capabilities.  

### **ReplicaSet & ReplicationController in Kubernetes**  

#### **1. Create a ReplicaSet**  
```bash
kubectl apply -f replicaset.yaml
```  

#### **2. List All ReplicaSets**  
```bash
kubectl get replicaset
```  

#### **3. Describe a ReplicaSet**  
```bash
kubectl describe replicaset my-replicaset
```  

#### **4. Scale a ReplicaSet**  
```bash
kubectl scale replicaset my-replicaset --replicas=5
```  

#### **5. Delete a ReplicaSet**  
```bash
kubectl delete replicaset my-replicaset
```  

#### **6. Get ReplicaSet YAML Definition**  
```bash
kubectl get replicaset my-replicaset -o yaml
```  
---
#### **7. Create a ReplicationController**  
```bash
kubectl apply -f replicationcontroller.yaml
```  

#### **8. List All ReplicationControllers**  
```bash
kubectl get rc
```  

#### **9. Describe a ReplicationController**  
```bash
kubectl describe rc my-replicationcontroller
```  

#### **10. Scale a ReplicationController**  
```bash
kubectl scale rc my-replicationcontroller --replicas=3
```  

#### **11. Delete a ReplicationController**  
```bash
kubectl delete rc my-replicationcontroller
```  

#### **12. Get ReplicationController YAML Definition**  
```bash
kubectl get rc my-replicationcontroller -o yaml
``` 
### **What is a Deployment in Kubernetes?**  
A **Deployment** in Kubernetes is used to manage and update applications by controlling **ReplicaSets**. It provides **rolling updates, rollbacks, and self-healing** capabilities, making it the preferred choice over ReplicaSets and ReplicationControllers.  

### **Key Features of Deployments:**  
- **Rolling Updates** → Updates Pods gradually to avoid downtime.  
- **Rollbacks** → Reverts to a previous version if something goes wrong.  
- **Scaling** → Easily scales Pods up or down.  
- **Self-Healing** → Automatically replaces failed Pods.  
- **Declarative Management** → Uses YAML to define the desired state.  

---

## **Deployment Commands**  

### **1. Create a Deployment**  
```bash
kubectl create deployment myapp --image=nginx
```

### **2. List All Deployments**  
```bash
kubectl get deployments
```

### **3. Get Detailed Deployment Information**  
```bash
kubectl describe deployment myapp
```

### **4. Get Deployment in a Specific Namespace**  
```bash
kubectl get deployments -n <namespace-name>
```

### **5. Update the Deployment Image**  
```bash
kubectl set image deployment myapp nginx=nginx:1.21
```

### **6. Rollback to the Previous Version**  
```bash
kubectl rollout undo deployment myapp
```

### **7. View Deployment Rollout Status**  
```bash
kubectl rollout status deployment myapp
```

### **8. Scale a Deployment (Increase Replicas)**  
```bash
kubectl scale deployment myapp --replicas=5
```

### **9. Delete a Deployment**  
```bash
kubectl delete deployment myapp
```

### **10. Get the YAML Definition of a Deployment**  
```bash
kubectl get deployment myapp -o yaml
```

### **11. Apply a Deployment from a YAML File**  
```bash
kubectl apply -f myapp-deployment.yaml
```

---

### **Rolling Update Example (Zero Downtime Update)**  
```bash
kubectl set image deployment myapp nginx=nginx:latest
kubectl rollout status deployment myapp
```
- Ensures smooth updates without stopping the application.  

---

### **Rollback Example**  
If the new version has issues, rollback to the previous version:  
```bash
kubectl rollout undo deployment myapp
```


| Feature                        | ReplicationController | ReplicaSet | Deployment |
|--------------------------------|----------------------|------------|------------|
| Ensures a fixed number of Pods | ✅ Yes               | ✅ Yes      | ✅ Yes      |
| Replaces failed Pods           | ✅ Yes               | ✅ Yes      | ✅ Yes      |
| Supports rolling updates       | ❌ No                | ❌ No       | ✅ Yes      |
| Supports rollback              | ❌ No                | ❌ No       | ✅ Yes      |
| Manages multiple ReplicaSets   | ❌ No                | ❌ No       | ✅ Yes      |
| Supports declarative updates   | ❌ No                | ✅ Yes      | ✅ Yes      |
| Recommended for production     | ❌ No                | ❌ No       | ✅ Yes      |


### **What is HPA (Horizontal Pod Autoscaler) in Kubernetes?**  
Horizontal Pod Autoscaler (**HPA**) automatically **scales the number of Pods** in a Deployment, ReplicaSet, or StatefulSet based on **CPU, memory, or custom metrics**.  

### **Key Features of HPA:**  
- **Automatic Scaling** → Increases or decreases the number of Pods based on resource usage.  
- **Works with Metrics Server** → Requires **Metrics Server** to collect CPU/memory usage.  
- **Supports Custom Metrics** → Can scale based on application-specific metrics.  
- **Improves Performance & Cost Efficiency** → Ensures applications get the required resources while avoiding over-provisioning.  

---

## **HPA Commands and Examples**  

### **1. Enable Metrics Server (If Not Installed)**
```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

### **2. Create a Deployment for HPA Testing**
```bash
kubectl create deployment myapp --image=nginx --requests=cpu=100m
```

### **3. Apply HPA to Scale Pods Based on CPU Usage**
```bash
kubectl autoscale deployment myapp --cpu-percent=50 --min=1 --max=10
```
- **`--cpu-percent=50`** → Scales Pods when CPU usage exceeds 50%.  
- **`--min=1` & `--max=10`** → Ensures the number of Pods stays between 1 and 10.  

### **4. View HPA Status**
```bash
kubectl get hpa
```

### **5. Check Current CPU Usage and Scaling Status**
```bash
kubectl top pod
```

### **6. Delete HPA**
```bash
kubectl delete hpa myapp
```

---

### **HPA YAML Example**
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: myapp-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: myapp
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```
- This configuration **scales the `myapp` Deployment between 1-10 Pods** when CPU usage exceeds **50%**.

---

## **HPA Based on Custom Metrics**
If your application exposes metrics (e.g., requests per second), you can use **custom metrics** with **Prometheus Adapter**. Example:
```yaml
metrics:
  - type: Pods
    pods:
      metric:
        name: http_requests_per_second
      target:
        type: AverageValue
        averageValue: 100m
```
- Scales Pods when HTTP requests exceed **100 per second**.

---

## **When to Use HPA?**
✅ Applications with **variable traffic** (e.g., web apps, APIs).  
✅ Ensuring **high availability** during peak loads.  
✅ Cost optimization by **scaling down** unused resources.  

