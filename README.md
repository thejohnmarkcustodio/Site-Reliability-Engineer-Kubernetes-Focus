# Kubernetes Troubleshooting Scenarios

## Section 1: Kubernetes Troubleshooting Scenarios

### 1. Pod CrashLoopBackOff

A developer informs you that a pod in the `foo` namespace is repeatedly crashing with the status `CrashLoopBackOff`. Describe the steps you would take to troubleshoot and resolve this issue. Include:
- Relevant `kubectl` commands.
- Logs or outputs you would check.
- Potential causes and resolutions.

**Answer:**

1. Check the status of the pod:
    ```sh
    kubectl get pod -n foo
    ```

2. Describe the pod to get more details:
    ```sh
    kubectl describe pod <pod-name> -n foo
    ```

3. Check the logs of the crashing container:
    ```sh
    kubectl logs <pod-name> -n foo --previous
    ```

4. Common causes and resolutions:
    - **Application Error**: Check the application logs for errors and fix the application code.
    - **Resource Limits**: Ensure the pod has enough CPU and memory resources.
    - **Configuration Issues**: Verify environment variables, config maps, and secrets.
    - **Dependency Failures**: Ensure all dependencies (e.g., databases) are available and accessible.

---

### 2. Node Not Ready

One of the nodes in your Kubernetes cluster shows a `NotReady` status. Outline the process to:
- Identify the root cause.
- Restore the node to a healthy state.
- Prevent similar issues in the future.

**Answer:**

1. Check the status of the nodes:
    ```sh
    kubectl get nodes
    ```

2. Describe the problematic node:
    ```sh
    kubectl describe node <node-name>
    ```

3. Check the kubelet logs on the node:
    ```sh
    journalctl -u kubelet -f
    ```

4. Common causes and resolutions:
    - **Network Issues**: Ensure the node can communicate with the control plane.
    - **Disk Pressure**: Free up disk space on the node.
    - **Memory Pressure**: Ensure the node has enough memory.
    - **Kubelet Issues**: Restart the kubelet service.

5. Preventive measures:
    - Monitor node health and resource usage.
    - Implement alerts for node issues.
    - Regularly update and maintain the nodes.

---

### 3. Service Unreachable

A service in the `bar` namespace is not reachable by other pods in the cluster. How would you:
- Diagnose the issue?
- Verify the service and networking configurations?
- Fix common problems related to this scenario?

**Answer:**

1. Check the status of the service:
    ```sh
    kubectl get svc -n bar
    ```

2. Describe the service to get more details:
    ```sh
    kubectl describe svc <service-name> -n bar
    ```

3. Check the endpoints of the service:
    ```sh
    kubectl get endpoints <service-name> -n bar
    ```

4. Verify the network policies:
    ```sh
    kubectl get networkpolicy -n bar
    ```

5. Common causes and resolutions:
    - **Pod Issues**: Ensure the pods backing the service are running and healthy.
    - **Service Selector**: Verify the service selector matches the pod labels.
    - **Network Policies**: Ensure network policies allow traffic to the service.
    - **DNS Issues**: Verify DNS resolution for the service.

---

### 4. High CPU/Memory Usage

A Kubernetes cluster is experiencing high CPU and memory usage on multiple nodes. Explain how you would:
- Identify the pods or processes causing resource strain.
- Optimize resource usage.
- Implement resource quotas and limits.

**Answer:**

1. Check the resource usage of nodes:
    ```sh
    kubectl top nodes
    ```

2. Check the resource usage of pods:
    ```sh
    kubectl top pods --all-namespaces
    ```

3. Identify the pods causing high resource usage:
    ```sh
    kubectl describe pod <pod-name> -n <namespace>
    ```

4. Optimize resource usage:
    - **Resource Requests and Limits**: Set appropriate resource requests and limits for pods.
    - **Horizontal Pod Autoscaler**: Use HPA to scale pods based on resource usage.
    - **Optimize Application**: Optimize the application code to use fewer resources.

5. Implement resource quotas and limits:
    ```yaml
    apiVersion: v1
    kind: ResourceQuota
    metadata:
      name: resource-quota
      namespace: <namespace>
    spec:
      hard:
        requests.cpu: "4"
        requests.memory: "8Gi"
        limits.cpu: "8"
        limits.memory: "16Gi"
    ```

---

## Section 2: Debugging Kubernetes Configurations

### 5. Misconfigured Ingress

An Ingress resource is defined, but traffic is not being routed correctly to the underlying pods. Provide steps to:
- Verify the Ingress resource configuration.
- Debug the Ingress controller logs.
- Identify and fix common misconfigurations.

**Answer:**

1. Check the status of the Ingress resource:
    ```sh
    kubectl get ingress -n <namespace>
    ```

2. Describe the Ingress resource to get more details:
    ```sh
    kubectl describe ingress <ingress-name> -n <namespace>
    ```

3. Check the Ingress controller logs:
    ```sh
    kubectl logs -l app=nginx-ingress-controller -n <namespace>
    ```

4. Common misconfigurations and resolutions:
    - **Backend Service**: Ensure the backend service and endpoints are correctly defined.
    - **Ingress Rules**: Verify the Ingress rules match the desired routing.
    - **TLS Configuration**: Ensure TLS certificates are correctly configured.
    - **DNS Configuration**: Verify DNS records point to the Ingress controller.
  
5. Misconfigured Ingress
An Ingress resource is defined, but traffic is not being routed correctly to the underlying pods. Provide steps to:
•	Verify the Ingress resource configuration.
•	Debug the Ingress controller logs.
•	Identify and fix common misconfigurations.
Answer:
1.	Check the status of the Ingress resource:
`kubectl get ingress -n <namespace>`
2.	Describe the Ingress resource to get more details:
`kubectl describe ingress <ingress-name> -n <namespace>`
3.	Check the Ingress controller logs:
`kubectl logs -l app=nginx-ingress-controller -n <namespace>`
4.	Common misconfigurations and resolutions:
o	Backend Service: Ensure the backend service and endpoints are correctly defined.
o	Ingress Rules: Verify the Ingress rules match the desired routing.
o	TLS Configuration: Ensure TLS certificates are correctly configured.
o	DNS Configuration: Verify DNS records point to the Ingress controller.

### 6. Persistent Volume Issues
A pod is stuck in a `Pending` state because it cannot bind to a Persistent Volume (PV). Describe the process to:

- **Investigate the issue:**
  1. Check the status of the Persistent Volume Claim (PVC) using `kubectl get pvc`.
  2. Describe the PVC to get more details using `kubectl describe pvc <pvc-name>`.
  3. Check the events section for any errors or warnings.

- **Verify the Persistent Volume and Persistent Volume Claim configurations:**
  1. List all Persistent Volumes using `kubectl get pv`.
  2. Describe the PV to ensure it matches the PVC requirements using `kubectl describe pv <pv-name>`.
  3. Ensure the storage class, access modes, and capacity match between the PV and PVC.

- **Resolve binding or provisioning issues:**
  1. If the PV is not available, create a new PV that matches the PVC requirements.
  2. If using dynamic provisioning, ensure the storage class is correctly configured and available.
  3. Check for any quota limits or resource constraints that might be preventing the binding.

---

### 7. DNS Resolution Failure
Pods in the cluster are unable to resolve external DNS names. How would you:

- **Confirm the issue:**
  1. Use `kubectl exec` to run a DNS query from within a pod, e.g., `kubectl exec -it <pod-name> -- nslookup google.com`.
  2. Check if the DNS query fails or times out.

- **Debug the cluster DNS setup:**
  1. Verify the CoreDNS pods are running using `kubectl get pods -n kube-system -l k8s-app=kube-dns`.
  2. Check the logs of the CoreDNS pods using `kubectl logs -n kube-system <coredns-pod-name>`.
  3. Ensure the CoreDNS configuration is correct by describing the ConfigMap: `kubectl describe configmap coredns -n kube-system`.

- **Restore DNS functionality:**
  1. If CoreDNS pods are not running, restart them using `kubectl rollout restart deployment coredns -n kube-system`.
  2. Correct any configuration issues in the CoreDNS ConfigMap.
  3. Ensure network policies or firewall rules are not blocking DNS traffic.

---
## Section 3: Practical Knowledge

### 8. YAML Configuration
Write a Kubernetes deployment manifest for a web application named `my-app` with the following requirements:

- **3 replicas**.
- **Image**: `nginx:1.23`.
- Expose the application on port **80**.
- Use a resource limit of **500m CPU** and **256Mi memory**.
- Pods should be scheduled to different nodes to ensure high availability.
- Ensure the pods are running across **3 Availability Zones (AZs)**.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app-container
        image: nginx:1.23
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: "500m"
            memory: "256Mi"
      topologySpreadConstraints:
      - maxSkew: 1
        topologyKey: topology.kubernetes.io/zone
        whenUnsatisfiable: DoNotSchedule
        labelSelector:
          matchLabels:
            app: my-app
```

### 9. Command Outputs
You are given the following `kubectl` command outputs:

- Output of `kubectl get pods -o wide`:
  ```
  NAME           READY   STATUS             RESTARTS   AGE   IP           NODE
  app-1          1/1     Running            0          3d    10.1.2.3     node-1
  app-2          0/1     CrashLoopBackOff   10         3d    10.1.2.4     node-1
  ```

- Output of `kubectl describe pod app-2`:
  ```
  Events:
    Type     Reason              Age               From               Message
    ----     ------              ----              ----               -------
    Normal   Pulled              1m (x5 over 10m)  kubelet, node-1    Successfully pulled image "app:2.0"
    Warning  BackOff             1m (x10 over 5m)  kubelet, node-1    Back-off restarting container
    Warning  MissingDependency   1m (x10 over 5m)  kubelet, node-1    Unable to locate dependency: libxyz.so

Based on these outputs, analyze the following:

- What could be causing the CrashLoopBackOff status? The CrashLoopBackOff status is likely caused by the missing shared library libxyz.so, as indicated in the events section of the kubectl describe pod app-2 output.

- What commands would you use to verify the container's environment?

1. `kubectl logs app-2` to check the container logs for any error messages.
2. `kubectl exec -it app-2 -- /bin/sh` to open a shell inside the container and manually inspect the filesystem and environment.
3. `docker inspect <image-id>` (if you have access to the Docker image locally) to check the image configuration.

- Provide a detailed plan to resolve the dependency issue:

1. Rebuild the image:
    - Modify the Dockerfile to include the missing libxyz.so library.
    - Build the new image using docker build -t app:2.1 ..
    - Push the new image to the container registry using docker push app:2.1.
2. Update the manifest:

    - Update the deployment manifest to use the new image app:2.1.
    - Apply the updated manifest using kubectl apply -f deployment.yaml.
3. Implement an init container (if necessary):

    - Add an init container to the deployment manifest to preload the libxyz.so library before the main container starts.
    - Example init container configuration:
```yaml
initContainers:
- name: init-myservice
  image: busybox
  command: ['sh', '-c', 'cp /path/to/libxyz.so /target/path']
  volumeMounts:
  - name: shared-data
    mountPath: /target/path
containers:
- name: my-app-container
  image: app:2.0
  volumeMounts:
  - name: shared-data
    mountPath: /target/path
volumes:
- name: shared-data
  emptyDir: {}
```

### 10. Scaling a Deployment
You have a deployment named api-server with 2 replicas. Traffic to the api-server has increased, and you need to scale it up to 5 replicas. Provide the steps and commands to perform this task.

Steps to scale the deployment:

Use the kubectl scale command to update the number of replicas.
Verify the scaling operation.
Commands:
- `kubectl scale deployment api-server --replicas=5`
- `kubectl get deployment api-server`
