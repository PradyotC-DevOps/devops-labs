# 1 - Deploy Pods in Kubernetes Cluster

## 📋 Task Overview

The Nautilus DevOps team is diving into Kubernetes for application management. One team member has a task to create a pod according to the details below:

- Create a pod named `pod-httpd` using the `httpd` image with the `latest` tag. Ensure to specify the tag as `httpd:latest`.
- Set the `app` label to `httpd_app`, and name the container as `httpd-container`.

---

## 🚀 Complete Solution

### 📝 1. Create the Pod YAML file

```bash
nano pod.yaml
```

### 📄 2. Pod Configuration (`pod.yaml`)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-httpd
  labels:
    app: httpd_app
spec:
  containers:
    - name: httpd-container
      image: httpd:latest
```

### 🚀 3. Deploy the Pod

```bash
kubectl apply -f pod.yaml
```

### ✅ 4. Verify the Deployment

```bash
kubectl get pod
```

**Expected Output:**

```
NAME        READY   STATUS    RESTARTS   AGE
pod-httpd   1/1     Running   0          8s
```