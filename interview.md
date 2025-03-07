# Interview Questions
1️⃣ How do you recover a lost Kubernetes cluster if the etcd data is corrupted?
✅ Answer:
Check if etcd snapshots were taken (etcdctl snapshot restore).
If a snapshot isn’t available, attempt disaster recovery by:
Scaling down control plane nodes.
Checking if any nodes still have valid etcd data.
Restoring from a secondary backup system.
If all else fails, rebuild the cluster and reapply workloads from stored YAML files or Helm charts.
2️⃣ Your Kubernetes nodes are in Ready state, but pods are not scheduling. What do you check?
✅ Answer:
kubectl describe node <node-name> → Look for taints & tolerations issues.
kubectl get events -A → Check for insufficient resources (CPU, memory).
kubectl get pods -o wide → Ensure pods are not stuck in Pending state due to scheduling constraints.
Node affinity rules (requiredDuringSchedulingIgnoredDuringExecution) could be misconfigured.
Check API server logs if nodes aren't communicating properly.
3️⃣ How do you troubleshoot intermittent network failures between Kubernetes pods?
✅ Answer:
Run kubectl exec -it <pod> -- ping <target-pod> to check connectivity.
Verify kubectl get pods -o wide to ensure pods are on the correct nodes.
kubectl describe networkpolicy to check if Network Policies are blocking traffic.
Check the CNI plugin (Calico, Flannel, Cilium) logs for any failures.
Use tcpdump or wireshark on the node to inspect dropped packets.
Enable Kubernetes Debugging with ephemeral containers (kubectl debug).
💡 Pro Tip: Always have etcd backups, proper monitoring (Prometheus, Grafana), and logging (ELK, Loki, Fluentd) in place for real-world troubleshooting!

