# 5 - Execute Rolling Updates in Kubernetes

## 📋 Task Overview

An application currently running on the Kubernetes cluster employs the nginx web server. The Nautilus application development team has introduced some recent changes that need deployment. They've crafted an image `nginx:1.17` with the latest updates. Execute a rolling update for this application, integrating the `nginx:1.17` image. The deployment is named `nginx-deployment`. Ensure all pods are operational post-update.

---

## 🚀 Complete Solution

### Step 1: Execute the Rolling Update

Update the container image of deployment `nginx-deployment` to `nginx:1.17`:

```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.17
```

**Terminal Output:**

```text
deployment.apps/nginx-deployment image updated
```

---

### Step 2: Monitor Rollout Status

Track the rollout progress to ensure new pods are spawned and old pods are safely terminated:

```bash
kubectl rollout status deployment/nginx-deployment
```

**Terminal Output:**

```text
Waiting for deployment "nginx-deployment" rollout to finish: 1 out of 3 new replicas have been updated...
Waiting for deployment "nginx-deployment" rollout to finish: 2 out of 3 new replicas have been updated...
Waiting for deployment "nginx-deployment" rollout to finish: 1 old replicas are pending termination...
deployment "nginx-deployment" successfully rolled out
```

---

### Step 3: Verify All Updated Resources

Check the active deployment, replicasets, and running pods:

```bash
kubectl get all
```

**Terminal Output:**

```text
NAME                                    READY   STATUS    RESTARTS   AGE
pod/nginx-deployment-55d59d6bfd-2q9lw   1/1     Running   0          45s
pod/nginx-deployment-55d59d6bfd-h8mks   1/1     Running   0          42s
pod/nginx-deployment-55d59d6bfd-x4vnt   1/1     Running   0          38s

NAME                 TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
service/kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   25d

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-deployment   3/3     3            3           10m

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-deployment-55d59d6bfd   3         3         3       45s
replicaset.apps/nginx-deployment-688975877c   0         0         0       10m
```