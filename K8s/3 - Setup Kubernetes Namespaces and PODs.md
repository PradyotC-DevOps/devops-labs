# 3 - Setup Kubernetes Namespaces and PODs

## 📋 Task Overview

The Nautilus DevOps team is organizing cluster resources and isolating workloads across different environments. To ensure proper resource separation, the team lead has requested setting up a dedicated Kubernetes Namespace and deploying an application Pod inside it.

Follow the specifications below to create the Namespace and deploy the target Pod:

1. Create a Kubernetes Namespace named `dev`.
2. Create a Pod named `nginx-pod` inside the `dev` namespace.
3. Use the container image `nginx:alpine` for the container inside the Pod.
4. Set container port to `80`.
5. Add the label `app: frontend` to the Pod configuration.
6. Verify that the Pod is successfully created and reaches the `Running` state in the `dev` namespace.

---

## 🚀 Complete Solution

### Step 1: Create the Kubernetes Namespace

Create the dedicated `dev` namespace imperatively using `kubectl`:

```bash
kubectl create namespace dev
```

**Terminal Output:**

```text
namespace/dev created
```

---

### Step 2: Deploy the Pod into the Namespace

Run the `nginx-pod` inside namespace `dev` with container port `80` and label `app=frontend`:

```bash
kubectl run nginx-pod --image=nginx:alpine --namespace=dev --port=80 --labels="app=frontend"
```

**Terminal Output:**

```text
pod/nginx-pod created
```

---

### Step 3: Verify Pod Status in the Namespace

Query all pods running inside namespace `dev` to verify the Pod status:

```bash
kubectl get pods -n dev -o wide
```

**Terminal Output:**

```text
NAME        READY   STATUS    RESTARTS   AGE   IP           NODE           NOMINATED NODE   READINESS GATES
nginx-pod   1/1     Running   0          18s   10.244.0.5   controlplane   <none>           <none>
```

---

### Step 4: Inspect Pod Details & Namespace Isolation

Inspect detailed pod metadata including labels, IP assignment, and namespace isolation:

```bash
kubectl describe pod nginx-pod -n dev
```

**Terminal Output (Snippet):**

```text
Name:         nginx-pod
Namespace:    dev
Priority:     0
Node:         controlplane/172.25.0.112
Labels:       app=frontend
Status:       Running
IP:           10.244.0.5
Containers:
  nginx-pod:
    Container ID:   containerd://e942f...
    Image:          nginx:alpine
    Port:           80/TCP
    State:          Running
      Started:      Fri, 24 Jul 2026 18:34:02 +0530
```