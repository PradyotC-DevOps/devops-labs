# 4 - Set Resource Limits in Kubernetes Pods

## 📋 Task Overview

The Nautilus DevOps team has noticed performance issues in some Kubernetes-hosted applications due to resource constraints. To address this, they plan to set limits on resource utilization.

Create a pod named `httpd-pod` with a container named `httpd-container` using image `httpd:latest` and set the following resource limits:

- Requests: Memory: 15Mi, CPU: 100m
- Limits: Memory: 20Mi, CPU: 100m

---

## 🚀 Complete Solution

### Step 1: Generate Dry-Run Manifest

Generate an initial pod specification using `kubectl run` with resource limits flags:

```bash
kubectl run httpd-pod --image=httpd:latest --requests="memory=15Mi,cpu=100m" --limits="memory=20Mi,cpu=100m" --dry-run=client -o yaml > pod.yaml
cat pod.yaml
```

**Terminal Output:**

```yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: httpd-pod
  name: httpd-pod
spec:
  containers:
  - image: httpd:latest
    name: httpd-pod
    resources:
      limits:
        cpu: 100m
        memory: 20Mi
      requests:
        cpu: 100m
        memory: 15Mi
status: {}
```

---

### Step 2: Modify Container Name in `pod.yaml`

Update the container name from `httpd-pod` to `httpd-container` as specified in the task requirement:

```bash
sed -i 's/name: httpd-pod/name: httpd-container/g' pod.yaml
cat pod.yaml
```

**Terminal Output:**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: httpd-pod
spec:
  containers:
  - image: httpd:latest
    name: httpd-container
    resources:
      limits:
        cpu: 100m
        memory: 20Mi
      requests:
        cpu: 100m
        memory: 15Mi
```

---

### Step 3: Apply Manifest & Verify Pod Resource Limits

Apply the configuration file to create the Pod:

```bash
kubectl apply -f pod.yaml
```

**Terminal Output:**

```text
pod/httpd-pod created
```

Verify Pod status and inspect resource requests/limits:

```bash
kubectl describe pod httpd-pod
```

**Terminal Output (Resource Details):**

```text
Name:         httpd-pod
Namespace:    default
Priority:     0
Node:         controlplane/172.25.0.112
Start Time:   Fri, 24 Jul 2026 18:35:10 +0530
Labels:       run=httpd-pod
Status:       Running
IP:           10.244.0.6
Containers:
  httpd-container:
    Container ID:   containerd://b8f91...
    Image:          httpd:latest
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Fri, 24 Jul 2026 18:35:12 +0530
    Ready:          True
    Limits:
      cpu:     100m
      memory:  20Mi
    Requests:
      cpu:     100m
      memory:  15Mi
```