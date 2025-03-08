## **DaemonSet & hostPath in Kubernetes**  

### **1️⃣ DaemonSet**  
A **DaemonSet** ensures that a specific **Pod runs on all (or some) Nodes** in a cluster.  

### ✅ **Use Cases:**  
- Running **logging** agents (e.g., Fluentd, Filebeat)  
- Deploying **monitoring** agents (e.g., Prometheus Node Exporter)  
- Running **security** or **networking** tools (e.g., Falco, Cilium)  

### 🔹 **Example: Running a DaemonSet for Node Monitoring**  
```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: node-exporter
spec:
  selector:
    matchLabels:
      app: node-exporter
  template:
    metadata:
      labels:
        app: node-exporter
    spec:
      containers:
      - name: node-exporter
        image: prom/node-exporter
```
🚀 **Effect:** This Pod will run on **every Node** in the cluster.

---

### **2️⃣ hostPath Volume**  
A **hostPath** volume mounts a file or directory from the **host node’s filesystem** into a Pod.  

### ✅ **Use Cases:**  
- Accessing **log files** stored on the Node  
- Using **Docker socket** for monitoring  
- Mounting **configuration files** from the Node  

### 🔹 **Example: Using hostPath for Log Collection**  
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: log-reader
spec:
  containers:
  - name: busybox
    image: busybox
    command: ["/bin/sh", "-c", "tail -f /var/log/syslog"]
    volumeMounts:
    - mountPath: /var/log
      name: log-volume
  volumes:
  - name: log-volume
    hostPath:
      path: /var/log
      type: Directory
```
🚀 **Effect:** This Pod will read logs from `/var/log` on the Node.

---

### 🎯 **DaemonSet + hostPath Example (Logging Agent)**  
```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: filebeat
spec:
  selector:
    matchLabels:
      app: filebeat
  template:
    metadata:
      labels:
        app: filebeat
    spec:
      containers:
      - name: filebeat
        image: elastic/filebeat
        volumeMounts:
        - name: logs
          mountPath: /var/log
      volumes:
      - name: logs
        hostPath:
          path: /var/log
          type: Directory
```
🚀 **Effect:** This ensures that **Filebeat runs on every Node** and collects logs from `/var/log`.  

---

### **🔥 Summary Table**  
| Feature | DaemonSet | hostPath |
|---------|----------|---------|
| Purpose | Ensures a Pod runs on all (or some) Nodes | Mounts a host node directory into a Pod |
| Common Use Case | Logging, monitoring, security agents | Accessing logs, config files, Docker socket |
| Scope | Cluster-wide | Node-specific |
| Example | Prometheus Node Exporter, Fluentd | Mounting `/var/log` for logging |

---

🔥 **Would you like a hands-on exercise to deploy a DaemonSet with hostPath?** 🚀