# **📌 ServiceAccount in Kubernetes**  

## **🔹 What is a ServiceAccount?**  
A **ServiceAccount** in Kubernetes is used to provide an identity to Pods so they can interact with the Kubernetes API or other services securely.  

- By default, Pods use the **default ServiceAccount** of the namespace.  
- You can create custom ServiceAccounts to provide **specific permissions** using **RBAC (Role-Based Access Control)**.  
- ServiceAccounts are often used to **authenticate Pods** with external services like AWS, GCP, and Vault.  

---

## **🛠 Create a Custom ServiceAccount**
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-service-account
  namespace: default
```
Apply the YAML file:
```bash
kubectl apply -f serviceaccount.yaml
```

---

## **🔹 Use ServiceAccount in a Pod**
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  serviceAccountName: my-service-account
  containers:
    - name: my-container
      image: nginx
```
- This Pod will use `my-service-account` instead of the **default ServiceAccount**.

---

## **🔹 Assign Permissions Using RBAC**
To allow the ServiceAccount to access Kubernetes resources, you need to create a **Role** or **ClusterRole** and bind it using a **RoleBinding** or **ClusterRoleBinding**.

### **📝 Example: Allow a ServiceAccount to List Pods**
#### **1️⃣ Create a Role**
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: default
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list"]
```
#### **2️⃣ Bind the Role to the ServiceAccount**
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-reader-binding
  namespace: default
subjects:
  - kind: ServiceAccount
    name: my-service-account
    namespace: default
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```
---
## **📌 How to Verify?**
### **🔍 Check All ServiceAccounts**
```bash
kubectl get serviceaccounts
```
### **🔍 Describe a ServiceAccount**
```bash
kubectl describe serviceaccount my-service-account
```
### **🔍 Check RoleBinding**
```bash
kubectl get rolebinding pod-reader-binding -o yaml
```
---
## **🔥 Key Takeaways**
✔ **ServiceAccounts provide identity** to Pods in Kubernetes.  
✔ By default, Pods use the **default** ServiceAccount.  
✔ Custom ServiceAccounts can be created for **better security**.  
✔ Use **RBAC (Role, RoleBinding)** to grant permissions.  
✔ ServiceAccounts are used for **secure API access & external authentication**.  

Do you need a hands-on practice exercise? 🚀

### **🔥 Hands-on Exercise: ServiceAccount in Kubernetes**  

In this exercise, we will:  
✅ Create a **ServiceAccount**.  
✅ Assign permissions using **Role & RoleBinding**.  
✅ Run a **Pod using the ServiceAccount**.  
✅ Test API access from within the Pod.

---

### **📌 Step 1: Create a ServiceAccount**  
Save the following YAML as `serviceaccount.yaml`:
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-service-account
  namespace: default
```
Apply it:
```bash
kubectl apply -f serviceaccount.yaml
```
Verify:
```bash
kubectl get serviceaccount
```

---

### **📌 Step 2: Create a Role with Read Access to Pods**  
Save the following YAML as `role.yaml`:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: default
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list"]
```
Apply it:
```bash
kubectl apply -f role.yaml
```
Verify:
```bash
kubectl get role
```

---

### **📌 Step 3: Bind the Role to the ServiceAccount**  
Save the following YAML as `rolebinding.yaml`:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-reader-binding
  namespace: default
subjects:
  - kind: ServiceAccount
    name: my-service-account
    namespace: default
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```
Apply it:
```bash
kubectl apply -f rolebinding.yaml
```
Verify:
```bash
kubectl get rolebinding
```

---

### **📌 Step 4: Create a Pod Using the ServiceAccount**  
Save the following YAML as `pod.yaml`:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  serviceAccountName: my-service-account
  containers:
    - name: busybox
      image: busybox
      command: ["/bin/sh", "-c", "sleep 3600"]
```
Apply it:
```bash
kubectl apply -f pod.yaml
```
Verify:
```bash
kubectl get pod
```

---

### **📌 Step 5: Test API Access from Inside the Pod**  
Get into the Pod:
```bash
kubectl exec -it my-pod -- sh
```
Inside the Pod, run:
```sh
curl -k -H "Authorization: Bearer $(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" \
  https://kubernetes.default.svc/api/v1/pods
```
If everything is correct, you should see a list of Pods.

---

### **📌 Step 6: Clean Up**
```bash
kubectl delete -f pod.yaml
kubectl delete -f rolebinding.yaml
kubectl delete -f role.yaml
kubectl delete -f serviceaccount.yaml
```

---

### **🎯 What Did You Learn?**
✔ Created a **ServiceAccount**.  
✔ Granted permissions using **RBAC (Role & RoleBinding)**.  
✔ Used the ServiceAccount in a **Pod**.  
✔ Verified **API access** from within the Pod.

Do you want a similar hands-on for ServiceAccounts in **AWS (IAM Role for ServiceAccounts - IRSA)?** 🚀

### **RBAC (Role-Based Access Control) in Kubernetes**  

RBAC is a security mechanism in Kubernetes that controls who can perform what actions on resources within a cluster. It uses **Roles and RoleBindings** to define permissions.

---

## **🔹 Key RBAC Components**
| Component | Scope | Purpose |
|-----------|--------|----------|
| **Role** | Namespace | Grants permissions to resources within a namespace. |
| **RoleBinding** | Namespace | Binds a Role to a user, group, or ServiceAccount within a namespace. |
| **ClusterRole** | Cluster-wide | Grants permissions to resources across the entire cluster. |
| **ClusterRoleBinding** | Cluster-wide | Binds a ClusterRole to a user, group, or ServiceAccount at the cluster level. |

---

## **📌 1. Role (Namespace-Level Permissions)**  
A **Role** defines permissions within a specific namespace.  

### **Example: Role that allows reading Pods in the "dev" namespace**  
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: pod-reader
  namespace: dev
rules:
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "list", "watch"]
```

---

## **📌 2. RoleBinding (Assigning a Role to a User or ServiceAccount)**  
A **RoleBinding** links a **Role** to a **user, group, or ServiceAccount**.

### **Example: Bind "pod-reader" Role to a ServiceAccount**
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: pod-reader-binding
  namespace: dev
subjects:
  - kind: ServiceAccount
    name: dev-user
    namespace: dev
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

---

## **📌 3. ClusterRole (Cluster-Wide Permissions)**  
A **ClusterRole** is similar to a Role but applies cluster-wide.  

### **Example: ClusterRole that allows viewing nodes**
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: node-viewer
rules:
  - apiGroups: [""]
    resources: ["nodes"]
    verbs: ["get", "list", "watch"]
```

---

## **📌 4. ClusterRoleBinding (Assigning a ClusterRole to a User or ServiceAccount)**  
A **ClusterRoleBinding** links a **ClusterRole** to a **user, group, or ServiceAccount** at the cluster level.

### **Example: Bind "node-viewer" ClusterRole to a ServiceAccount**
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: node-viewer-binding
subjects:
  - kind: ServiceAccount
    name: cluster-user
    namespace: kube-system
roleRef:
  kind: ClusterRole
  name: node-viewer
  apiGroup: rbac.authorization.k8s.io
```

---

## **📌 Key Differences**
| Feature | Role | RoleBinding | ClusterRole | ClusterRoleBinding |
|---------|------|------------|-------------|---------------------|
| Scope | Namespace | Namespace | Cluster-wide | Cluster-wide |
| Controls Access To | Namespaced resources | Namespaced users | Cluster-wide resources | Cluster-wide users |
| Can Grant Access To | Users, ServiceAccounts | Users, ServiceAccounts | Users, ServiceAccounts | Users, ServiceAccounts |

---

### **🛠️ Verify RBAC Rules**
1️⃣ **Check roles in a namespace:**
```bash
kubectl get role -n dev
kubectl get rolebinding -n dev
```
2️⃣ **Check cluster-wide roles:**
```bash
kubectl get clusterrole
kubectl get clusterrolebinding
```
3️⃣ **Check permissions for a user:**
```bash
kubectl auth can-i list pods --as=system:serviceaccount:dev:dev-user -n dev
```

---

## **✅ Summary**
- **Roles & RoleBindings** → Namespace-specific access control.
- **ClusterRoles & ClusterRoleBindings** → Cluster-wide access control.
- **RBAC helps enforce the principle of least privilege** by granting only necessary permissions.

Let me know if you need further explanation! 🚀

### **Network Policy in Kubernetes** 🚀  

#### **What is a Network Policy?**
A **NetworkPolicy** in Kubernetes controls **incoming and outgoing traffic** between **Pods** based on rules. It helps in **restricting communication** between services inside a cluster, enhancing security.

By default, **all Pods can communicate with each other** in Kubernetes. Using **NetworkPolicy**, we can restrict which Pods can talk to each other.

---

### **Key Features**
✅ Controls **Ingress (incoming)** & **Egress (outgoing)** traffic.  
✅ Uses **labels** to define rules for Pod communication.  
✅ Works only with **network plugins** that support NetworkPolicies (e.g., Calico, Cilium).  
✅ Applies to **Pods, not Services**.

---

### **Example: Deny All Incoming Traffic to a Pod**
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all
  namespace: default
spec:
  podSelector: {}  # Selects all Pods in the namespace
  policyTypes:
    - Ingress
```
🚀 **Effect:** All incoming traffic to all Pods in the `default` namespace is blocked.

---

### **Example: Allow Only Frontend to Talk to Backend**
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-backend
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
    - Ingress
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
```
🚀 **Effect:**  
- Only Pods with label `app=frontend` can send requests to `app=backend`.  
- Other Pods cannot talk to `backend`.

---

### **Example: Allow All Outgoing Traffic from a Pod**
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-all-egress
  namespace: default
spec:
  podSelector:
    matchLabels:
      app: mypod
  policyTypes:
    - Egress
  egress:
    - {}
```
🚀 **Effect:**  
- Pods with `app=mypod` can send traffic **anywhere** (inside or outside the cluster).  
- Ingress is **not affected** by this rule.

---

### **When to Use Network Policies?**
✔️ **Restrict traffic** between namespaces or microservices.  
✔️ **Prevent unauthorized access** to sensitive services like databases.  
✔️ **Improve security** in multi-tenant environments.  
✔️ **Apply zero-trust networking** within Kubernetes.  

---

### **Important Notes**
- **NetworkPolicies only affect Pods, not external access** (e.g., LoadBalancers).  
- **Default behavior:** If no NetworkPolicy exists, all traffic is allowed.  
- **Only works with network plugins** that support policies (Calico, Cilium, etc.).  

Let me know if you need more details or practical exercises! 🚀