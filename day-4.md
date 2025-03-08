### **What is a ConfigMap in Kubernetes?**  

A **ConfigMap** in Kubernetes is used to store **configuration data** separately from application code. It allows you to manage environment-specific settings **without modifying container images**.  

🔹 **Key Features:**  
- Decouples configuration from application code.  
- Stores key-value pairs, entire files, or environment variables.  
- Used to configure applications dynamically.  
- Can be mounted as a volume or injected as environment variables.  

---

## **1️⃣ Creating a ConfigMap**
You can create a ConfigMap in multiple ways:  

### **1.1 Using a YAML File**  
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-config
data:
  database_url: "mysql://db-service:3306"
  log_level: "info"
```
📌 **Explanation:**  
- **`data`** → Stores key-value pairs (e.g., `database_url`, `log_level`).  

📌 **Apply the ConfigMap:**
```bash
kubectl apply -f my-config.yaml
```

---

### **1.2 Using the `kubectl create configmap` Command**  
```bash
kubectl create configmap my-config --from-literal=database_url="mysql://db-service:3306" --from-literal=log_level="info"
```
📌 **This creates the same ConfigMap as the YAML file.**

---

## **2️⃣ Using a ConfigMap in a Pod**  

### **2.1 Inject as Environment Variables**  
Modify the Pod spec to use the ConfigMap:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app
spec:
  containers:
  - name: app-container
    image: busybox
    env:
    - name: DATABASE_URL
      valueFrom:
        configMapKeyRef:
          name: my-config
          key: database_url
```
📌 **How it Works?**  
- Injects `database_url` from `my-config` as the environment variable `DATABASE_URL`.  

---

### **2.2 Mount as a Volume in a Pod**  
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app
spec:
  volumes:
  - name: config-volume
    configMap:
      name: my-config
  containers:
  - name: app-container
    image: busybox
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
```
📌 **How it Works?**  
- The ConfigMap values will be available as files inside `/etc/config/`.  

---

## **3️⃣ Get & Manage ConfigMaps**  

### **List All ConfigMaps**  
```bash
kubectl get configmaps
```

### **View a ConfigMap**  
```bash
kubectl get configmap my-config -o yaml
```

### **Edit a ConfigMap**  
```bash
kubectl edit configmap my-config
```

### **Delete a ConfigMap**  
```bash
kubectl delete configmap my-config
```

---

### **Summary**
| **Feature** | **ConfigMap** |
|------------|-------------|
| Stores Configuration Data | ✅ Yes |
| Stores Secrets | ❌ No (Use Secrets) |
| Mounted as a Volume | ✅ Yes |
| Used as Environment Variables | ✅ Yes |
| Dynamic Updates | ✅ Yes |

🔹 **ConfigMaps help separate configuration from code, making applications more flexible and manageable.** 🚀

### **What is a Secret in Kubernetes?**  

A **Secret** in Kubernetes is used to store **sensitive data** such as passwords, API keys, TLS certificates, and database credentials **securely**. Unlike ConfigMaps, Secrets **are encoded in Base64** and provide a more secure way to handle sensitive information.  

---

## **1️⃣ Creating a Secret**  

### **1.1 Using a YAML File**
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: my-secret
type: Opaque
data:
  username: dXNlcm5hbWU=  # Base64 encoded "username"
  password: cGFzc3dvcmQ=  # Base64 encoded "password"
```
📌 **Explanation:**  
- `data` → Stores key-value pairs in Base64 format.  
- `type: Opaque` → General-purpose secret type.  

📌 **Apply the Secret:**
```bash
kubectl apply -f my-secret.yaml
```

---

### **1.2 Creating a Secret Using `kubectl`**
```bash
kubectl create secret generic my-secret --from-literal=username="admin" --from-literal=password="mypassword"
```
📌 **Kubernetes automatically encodes the values in Base64.**

---

## **2️⃣ Using a Secret in a Pod**  

### **2.1 Inject as Environment Variables**  
Modify the Pod spec to use the Secret:  
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app
spec:
  containers:
  - name: app-container
    image: busybox
    env:
    - name: USERNAME
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: username
    - name: PASSWORD
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: password
```
📌 **How it Works?**  
- The Secret values are injected as environment variables `USERNAME` and `PASSWORD`.  

---

### **2.2 Mount as a Volume in a Pod**  
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app
spec:
  volumes:
  - name: secret-volume
    secret:
      secretName: my-secret
  containers:
  - name: app-container
    image: busybox
    volumeMounts:
    - name: secret-volume
      mountPath: "/etc/secret"
```
📌 **How it Works?**  
- The Secret values are mounted as files inside `/etc/secret/`.  

---

## **3️⃣ Get & Manage Secrets**  

### **List All Secrets**  
```bash
kubectl get secrets
```

### **View a Secret (Encoded)**  
```bash
kubectl get secret my-secret -o yaml
```

### **Decode a Secret Value**  
```bash
echo "dXNlcm5hbWU=" | base64 --decode
```

### **Edit a Secret**  
```bash
kubectl edit secret my-secret
```

### **Delete a Secret**  
```bash
kubectl delete secret my-secret
```

---

## **4️⃣ Secret Types in Kubernetes**  
| **Secret Type** | **Description** |
|---------------|---------------|
| `Opaque` | Default type for key-value pairs. |
| `kubernetes.io/dockerconfigjson` | Stores Docker registry credentials. |
| `kubernetes.io/tls` | Stores TLS certificates (`tls.crt` and `tls.key`). |
| `bootstrap.kubernetes.io/token` | Used for Kubernetes bootstrap tokens. |

---

## **Summary**  
| **Feature** | **ConfigMap** | **Secret** |
|------------|-------------|-------------|
| Stores Sensitive Data | ❌ No | ✅ Yes |
| Stores General Config | ✅ Yes | ❌ No |
| Values are Base64 Encoded | ❌ No | ✅ Yes |
| Mounted as a Volume | ✅ Yes | ✅ Yes |
| Used as Environment Variables | ✅ Yes | ✅ Yes |
| More Secure | ❌ No | ✅ Yes |

🔹 **Use Secrets for sensitive data to keep applications secure.** 🚀

# **Probes in Kubernetes**

Probes in Kubernetes are used to **check the health of a container** inside a Pod. Kubernetes uses probes to determine whether a container is **ready to serve traffic** or needs to be **restarted**.

---

## **1️⃣ Types of Probes**  

| **Probe Type** | **Purpose** | **What Happens if it Fails?** |
|--------------|------------|----------------|
| **Liveness Probe** | Checks if the container is alive (not stuck/crashed). | The container is **restarted**. |
| **Readiness Probe** | Checks if the container is ready to serve requests. | The Pod is **removed from service** (no traffic sent). |
| **Startup Probe** | Checks if the container has **fully started** before other probes run. | The container is restarted **only** if it fails before startup completes. |

---

## **2️⃣ Implementing Probes in Kubernetes**

### **2.1 Liveness Probe (Check if Container is Alive)**
This example uses an **HTTP request** to check if the container is running.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: liveness-probe-example
spec:
  containers:
  - name: my-container
    image: busybox
    livenessProbe:
      httpGet:
        path: /health
        port: 8080
      initialDelaySeconds: 3
      periodSeconds: 5
```
📌 **Explanation**  
- `httpGet` → Sends an HTTP request to `/health` on port `8080`.  
- `initialDelaySeconds: 3` → Waits 3 seconds before the first probe.  
- `periodSeconds: 5` → Checks every 5 seconds.  
- If this check **fails**, the container is restarted.  

---

### **2.2 Readiness Probe (Check if Container is Ready)**
The following example uses a **TCP probe** to check if the container is ready to serve requests.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: readiness-probe-example
spec:
  containers:
  - name: my-container
    image: busybox
    readinessProbe:
      tcpSocket:
        port: 3306
      initialDelaySeconds: 5
      periodSeconds: 3
```
📌 **Explanation**  
- `tcpSocket` → Checks if the application is listening on **port 3306**.  
- If this probe **fails**, the Pod **stays in "Not Ready" state** and does not receive traffic.  

---

### **2.3 Startup Probe (Check if Container has Fully Started)**
Used when the application **takes a long time** to start.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: startup-probe-example
spec:
  containers:
  - name: my-container
    image: busybox
    startupProbe:
      exec:
        command: ["cat", "/app/started"]
      initialDelaySeconds: 10
      periodSeconds: 5
      failureThreshold: 10
```
📌 **Explanation**  
- `exec` → Runs a command inside the container (`cat /app/started`).  
- `initialDelaySeconds: 10` → Waits 10 seconds before the first check.  
- `failureThreshold: 10` → Allows up to 10 failures before restarting.  
- If this probe **fails**, the container is **restarted** until the application is fully started.  

---

## **3️⃣ Probe Methods**  

| **Probe Method** | **Description** | **Example** |
|-----------------|---------------|-----------|
| **HTTP Probe** | Sends an HTTP request to a container. | `httpGet` |
| **TCP Probe** | Checks if a TCP port is open. | `tcpSocket` |
| **Command Probe** | Runs a command inside the container. | `exec` |

---

## **4️⃣ How to Check Probe Status?**
```bash
kubectl describe pod <pod-name>
```
Look for `Liveness`, `Readiness`, or `Startup` probe events.

---

## **5️⃣ Summary**
| **Feature** | **Liveness Probe** | **Readiness Probe** | **Startup Probe** |
|------------|----------------|----------------|----------------|
| Purpose | Checks if the container is **alive** | Checks if the container is **ready** to serve traffic | Checks if the container has **fully started** |
| Failure Result | Container **restarts** | Pod is **removed from Service** | Container **restarts** |
| Used for | Detecting crashes | Controlling traffic flow | Applications with **long startup times** |

🚀 **Probes help improve the reliability of applications in Kubernetes by ensuring that only healthy and ready containers serve traffic.**

