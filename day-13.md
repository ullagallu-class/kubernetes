## **Monitoring in Kubernetes using Prometheus & Grafana**  

Monitoring is essential in Kubernetes to track performance, health, and resource usage of nodes, pods, and applications. **Prometheus** and **Grafana** are widely used for observability in Kubernetes.  

---

## **1️⃣ Prometheus - Monitoring & Alerting**  

Prometheus is a **time-series database and monitoring system** designed for Kubernetes. It collects and stores metrics using a pull-based model via **exporters**.

### ✅ **Prometheus Components**
- **Prometheus Server** – Collects and stores metrics.
- **Node Exporter** – Captures node-level metrics (CPU, memory, disk).
- **Kube-State-Metrics** – Provides Kubernetes object-level metrics (Deployments, Pods, Nodes).
- **cAdvisor** – Monitors resource usage of containers.
- **Alertmanager** – Sends alerts based on Prometheus metrics.

---

## **2️⃣ Grafana - Visualization Tool**  

Grafana is used to visualize Prometheus data with **dashboards** and **alerts**.  
It connects to Prometheus as a **data source** and provides real-time monitoring.

---

## **3️⃣ Deploying Prometheus & Grafana using Helm**  

### ✅ **Step 1: Install the Prometheus Stack**
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install prometheus-stack prometheus-community/kube-prometheus-stack
```
- This installs **Prometheus, Grafana, Alertmanager, and required exporters**.

### ✅ **Step 2: Check Installed Resources**
```bash
kubectl get pods -n default
kubectl get svc -n default
```
- This shows running Prometheus & Grafana services.

### ✅ **Step 3: Access Prometheus & Grafana**
```bash
kubectl port-forward svc/prometheus-stack-grafana 3000:80 -n default
```
- Open **http://localhost:3000**  
- Default **username/password:** `admin/prom-operator`

---

## **4️⃣ Node Exporter (EC2 Node Monitoring)**  

Node Exporter is used to monitor **EC2 instance metrics** like CPU, memory, and disk.

### ✅ **Install Node Exporter as a DaemonSet**
```bash
kubectl apply -f https://raw.githubusercontent.com/prometheus/node_exporter/master/examples/daemonset/node-exporter-daemonset.yaml
```

### ✅ **Access Node Exporter Metrics**
```bash
kubectl port-forward daemonset/node-exporter 9100:9100
curl http://localhost:9100/metrics
```
- Shows CPU, memory, and disk usage.

---

## **5️⃣ Adding EC2 Labels for Dynamic Node Monitoring**  

To dynamically label EC2 nodes for monitoring:
1. Add EC2 instance labels:
```bash
aws ec2 create-tags --resources i-xxxxxxxxx --tags Key=Environment,Value=Production
```

2. Use **Node Labels** in Kubernetes:
```bash
kubectl label node <node-name> environment=production
```

3. Modify **Prometheus scrape config** to discover labeled nodes:
```yaml
- job_name: 'node-exporter'
  ec2_sd_configs:
    - region: us-east-1
  relabel_configs:
    - source_labels: [__meta_ec2_tag_Environment]
      regex: Production
      action: keep
```

4. Apply the configuration and restart Prometheus.

---

## **6️⃣ Create Grafana Dashboards for EC2 Monitoring**  

### ✅ **Step 1: Add Prometheus as a Data Source in Grafana**
- Open Grafana → Go to `Settings` → `Data Sources`
- Add **Prometheus URL** (`http://prometheus-stack-prometheus.default.svc:9090`)

### ✅ **Step 2: Import a Node Exporter Dashboard**
- Go to **Dashboards** → `Import`
- Use **Dashboard ID:** `1860` (Node Exporter Dashboard)

Now, you can **monitor EC2 instances dynamically** using Prometheus & Grafana. 🚀  

---

This setup ensures **comprehensive monitoring** for both Kubernetes clusters and EC2 instances. Let me know if you need **custom queries, alerts, or troubleshooting help!** 🚀