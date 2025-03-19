# Day-1[kubeadm][12-03-2024]
- What and Why k8s
- K8s architecture
- Pod
  - initContainers
- Namespaces
- Labels & Selectors
# Day-2[kubeadm][13-03-2024]
- Replicaset & Replication Controller
- Deployment
- Resource Management
- Metrics Server
- HPA
- Job & CronJob
# Day-3[kubeadm][14-03-2024]
- StatefulSet
- Services[ClusterIP,NodePort,Headless,ExternalService]
- CoreDNS pods
- kube-dns service
# Day-4[kubeadm][15-03-2024]
- ConfigMap
- Secrets
- Probes
# Day-5[kubeadm][17-03-2024]
- Dynamic & Static Volume Provisioning[EBS CSI Driver Installation&PodIdentity]
  - volumes reclaims
  - accessMode
# Day-6[kubeadm]
- expense and instana manifestation
# Day-7[kubeadm]
- Helmify Expense and Instana
# Day-8[kubeadm]
- NodeSelector
- Affinity & Anti Affinity
- Taints & Tolerations
# Day-9[EKS]
- ServiceAccount
- RBAC
- Network Policy
# Day-10[EKS]
- LoadBalancer Service
- Ingress
- External DNS
- Annotations
# Day-11[EKS]
- Resource Quotas
- Limit Ranges
- HV
- ESO
# Day-12
- Monitoring[Proemtheus&Grafana]
- NodeExporter
- ec2 labels to dynamically monitor nodes
# Day-13
- Logging
- Daemonset
- hostPath
# Day-14
- Terraform way to create cluster
# Day-16[maybe]
- ISTIO[SideCar & Envoy]
# Day-17
- Karpenter
- ArGoCD
# Day-18
- Cluster Upgradation 






# Project 1[6 micro services[Instana]]
Deployment Environment: EKS
Programming Languages: Java[1],NodeJs[3],Python[1],ReactJs[frontend]

- Setup EKS cluster using terraform
  modules:
  - VPC
  - SG:
     - Control Plane
     - Node Group
     - ALB
  - IAM Groups and Users for EKS 
  - Pod Identitys
  - EKS CLuster[ControlPlane+NodeGroup]
  - ALB
  - CloudFront
  - WAF
  CRDS installation using Terraform
  - ALB Ingress Controller
  - External Secret Operator
  - External DNS Controller
- Scanning helm charts and terraform config files using trivy for vulenrabilities

- setup CI/CD pipelines to deploy app[DevSecOps]
  CI steps:
  - Declarative Checkout
  - Owasp Dependency Check
  - Docker build
  - Image Scanning
  - Image Push
  for CI -- using Jenkins CD Github Actions

- for secrets managment i'm using HashiCorp Vault
- Setup Montoring for CI/CD 
- Setup Montoring Using Prometheus and Grafana
- Setup Elastic Stack for Logging
- Istio

