# 2 - Deploy Applications with Kubernetes Deployments

## 📋 Task Overview

Create a deployment named `nginx` to deploy the application nginx using the image `nginx:latest` (ensure to specify the tag).

---

## 🚀 Complete Solution

### Step 1: Generate Deployment Manifest

Generate a dry-run YAML manifest for the deployment to review its specifications:

```bash
kubectl create deployment nginx --image=nginx:latest --dry-run=client -o yaml > dep.yaml
cat dep.yaml
```

**Terminal Output:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: nginx
  name: nginx
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx:latest
        name: nginx
        resources: {}
status: {}
```

---

### Step 2: Apply the Deployment Manifest

Deploy the application into the cluster by applying `dep.yaml`:

```bash
kubectl apply -f dep.yaml
```

**Terminal Output:**

```text
deployment.apps/nginx created
```

---

### Step 3: Verify Deployment and Resources

Check the status of the deployment, replicaset, and pods:

```bash
kubectl get all
```

**Terminal Output:**

```text
NAME                         READY   STATUS    RESTARTS   AGE
pod/nginx-77b4fdf86c-x9k2p   1/1     Running   0          12s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   25d

NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx   1/1     1            1           12s

NAME                               DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-77b4fdf86c   1         1         1       12s
```