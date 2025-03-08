# Helmification of expense and instana
## **What is Helm?**  

Helm is a **package manager for Kubernetes** that helps deploy, manage, and update applications efficiently using **Helm charts**. It simplifies Kubernetes configuration management by allowing you to templatize manifests and manage them as a single unit.

---

## **Why Use Helm?**  

### 🔹 **1. Reusability**  
- Instead of writing multiple YAML files for different environments, Helm **templates** them, making deployment easier.  

### 🔹 **2. Version Control**  
- Helm provides **rollback support**, so you can revert to previous versions if something goes wrong.  

### 🔹 **3. Parameterization**  
- You can customize deployments using **values.yaml** instead of modifying YAML files manually.  

### 🔹 **4. Dependency Management**  
- Helm allows managing dependencies (e.g., deploying databases along with applications).  

---

## **Key Helm Concepts**  

### **1️⃣ Helm Chart**  
A Helm chart is a **packaged application** for Kubernetes, containing:  
- **Templates** (Kubernetes manifests with placeholders)  
- **values.yaml** (Configurable parameters)  
- **Chart.yaml** (Metadata like name, version, dependencies)  

### **2️⃣ Helm Repository**  
A collection of Helm charts that can be shared and deployed. Example repositories:  
- **Helm Hub**  
- **Bitnami Charts**  
- **ArtifactHub**  

### **3️⃣ Helm Release**  
A deployed instance of a Helm chart in a Kubernetes cluster.  
- Each time a Helm chart is installed, Helm creates a **release**.  
- You can **rollback**, **upgrade**, or **delete** releases easily.  

---

## **Basic Helm Commands**  

### **🔹 Install a Helm Chart**  
```bash
helm install my-app bitnami/nginx
```
This deploys the `nginx` chart from the Bitnami repository with the release name `my-app`.

### **🔹 List Installed Releases**  
```bash
helm list
```

### **🔹 Upgrade a Release**  
```bash
helm upgrade my-app bitnami/nginx
```

### **🔹 Uninstall a Release**  
```bash
helm uninstall my-app
```

### **🔹 Rollback a Release**  
```bash
helm rollback my-app 1
```
(Reverts `my-app` to revision `1`)

---

## **Example Helm Chart Structure**  

```
my-chart/
│── Chart.yaml          # Chart metadata (name, version, description)
│── values.yaml         # Default values for templates
│── templates/          # Kubernetes manifest templates
│   ├── deployment.yaml # Deployment template
│   ├── service.yaml    # Service template
│   ├── ingress.yaml    # Ingress template
```

### ✅ **Example `values.yaml`**
```yaml
replicaCount: 3
image:
  repository: nginx
  tag: latest
service:
  type: ClusterIP
  port: 80
```

### ✅ **Example `deployment.yaml` (Template)**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ .Release.Name }}
spec:
  replicas: {{ .Values.replicaCount }}
  template:
    spec:
      containers:
      - name: nginx
        image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
```

This allows **dynamic configurations** by changing `values.yaml`.

---

## **🔥 Why Helm Over Plain YAML?**  

| Feature | Plain YAML | Helm |
|---------|-----------|------|
| **Reusability** | No | ✅ Yes |
| **Parameterization** | No | ✅ Yes (values.yaml) |
| **Version Control** | Manual | ✅ Built-in rollback |
| **Dependency Management** | Manual | ✅ Yes |

Helm **saves time**, **reduces complexity**, and **makes Kubernetes applications more manageable**. 🚀  

Let me know if you need help with **creating or deploying Helm charts!**