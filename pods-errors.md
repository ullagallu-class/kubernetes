Here’s a list of common Kubernetes (k8s) pod errors, their causes, and how to troubleshoot them:  

### 1. **CrashLoopBackOff**  
   - **Cause:** The container inside the pod keeps crashing and Kubernetes is restarting it in a loop.  
   - **Possible Reasons:**  
     - Application crashes due to an unhandled exception.  
     - Missing dependencies or misconfiguration.  
     - Insufficient resources (CPU/Memory limits exceeded).  
     - Incorrect command or entrypoint in Dockerfile.  
     - Failing health checks.  
   - **Fix:** Check logs (`kubectl logs <pod-name>`), describe the pod (`kubectl describe pod <pod-name>`), and fix the underlying issue.

---

### 2. **ImagePullBackOff / ErrImagePull**  
   - **Cause:** Kubernetes is unable to pull the container image.  
   - **Possible Reasons:**  
     - Incorrect image name or tag.  
     - Image doesn’t exist in the container registry.  
     - Authentication failure (for private registries).  
     - Network connectivity issues.  
   - **Fix:** Verify image name (`kubectl describe pod <pod-name>`), check Docker registry credentials, and ensure the image is accessible.

---

### 3. **CreateContainerConfigError**  
   - **Cause:** Pod creation failed due to an invalid configuration.  
   - **Possible Reasons:**  
     - Invalid environment variables or volume mounts.  
     - Secret or ConfigMap not found.  
     - Insufficient permissions (RBAC issues).  
   - **Fix:** Check the pod’s configuration (`kubectl describe pod <pod-name>`), verify secrets and ConfigMaps exist.

---

### 4. **CreateContainerError**  
   - **Cause:** The container failed to start.  
   - **Possible Reasons:**  
     - Issues with the container runtime.  
     - Invalid filesystem mounts.  
     - Insufficient node resources.  
   - **Fix:** Check logs and events using `kubectl describe pod <pod-name>` and investigate container runtime errors.

---

### 5. **OOMKilled (Out of Memory Killed)**  
   - **Cause:** The container used more memory than allocated and was killed by Kubernetes.  
   - **Possible Reasons:**  
     - Memory-intensive application without limits.  
     - Java applications without proper heap size settings.  
     - Insufficient node resources.  
   - **Fix:** Set appropriate memory requests/limits in pod spec and optimize application memory usage.

---

### 6. **RunContainerError**  
   - **Cause:** The container failed to start running.  
   - **Possible Reasons:**  
     - Invalid entrypoint or command in the Dockerfile.  
     - Executable permission issues in the container.  
     - Required files or dependencies missing inside the container.  
   - **Fix:** Check logs and verify the command works inside the container.

---

### 7. **Evicted**  
   - **Cause:** Kubernetes removed the pod due to resource pressure on the node.  
   - **Possible Reasons:**  
     - Node running out of memory or disk space.  
     - Quality of Service (QoS) policies favor other pods.  
   - **Fix:** Check node status (`kubectl describe node <node-name>`), increase resource limits, or add more nodes.

---

### 8. **NodeNotReady**  
   - **Cause:** The node where the pod is scheduled is not ready.  
   - **Possible Reasons:**  
     - Node is out of resources.  
     - Network connectivity issues.  
     - Kubelet or container runtime failure.  
   - **Fix:** Check node status (`kubectl get nodes`), restart kubelet, and check network connectivity.

---

### 9. **DeadlineExceeded**  
   - **Cause:** The pod failed to start within the specified deadline.  
   - **Possible Reasons:**  
     - Slow container startup.  
     - Network issues.  
     - Insufficient node resources.  
   - **Fix:** Check logs and node resource availability.

---

### 10. **Pending**  
   - **Cause:** The pod is stuck in the Pending state.  
   - **Possible Reasons:**  
     - No available nodes match pod resource requests.  
     - Scheduling constraints (e.g., nodeSelector, taints, tolerations).  
     - Persistent Volume claims are not bound.  
   - **Fix:** Use `kubectl describe pod <pod-name>` to check scheduling issues and fix them accordingly.

---

### 11. **ContainerCreating**  
   - **Cause:** The pod is stuck in the ContainerCreating state.  
   - **Possible Reasons:**  
     - Image pull is taking too long.  
     - Issues with volume mounts.  
     - Network plugin issues.  
   - **Fix:** Check events in `kubectl describe pod <pod-name>` and investigate node-level issues.

---

### 12. **ErrImageNeverPull**  
   - **Cause:** Kubernetes is configured to never pull the image, but the image is not available on the node.  
   - **Possible Reasons:**  
     - `imagePullPolicy: Never` is set, and the image isn’t present locally.  
   - **Fix:** Change `imagePullPolicy` to `Always` or `IfNotPresent`, or ensure the image exists on the node.

---

# Errors in pod step by step[Pod status]
`stage1:`
kubectl apply -f deployment.yaml received by API server checks auth and authorization and validates the manifest file once everything is passed store into etcd now the status is `pod is assigned` status

`stage2` scheduler responsible for find best fit node for 
- pod assigned
- pod not scheduled
- pod scheduled container creation error
- pod schduled container runtime error

**pending**[pod not getting assigned/not scheduled][for debugging such pending issues we can do kubectl describe]
- insufficient memory
- did not match nodeaffinity/nodeselector
- unbound pv
- node is tainted

**pod assigned/scheduled**[for debugging such below errors kubectl logs]
- unable to pull image due to lack of permissions,image not available
- configmap to secrets wrong,names mismatch
- desired rs is 3 but it will create only 2 i.e reason is resource quota
- crashloopbackoff container creation failed so container creation started again, failed ,start this is loop so 
  - OOM out of memory[talk to QA to get how much memeory needed]
  - Health Check fails[talk to dev to team any change in path or port]
  - Init container not completed successfully
  - connections issues

- application not able to access
 - mismatch between service and pod[issues n/w or communications issue]

