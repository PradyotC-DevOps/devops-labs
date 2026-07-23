# 12 - Update Deployment and Service in Kubernetes

## 📋 Task Overview

An application deployed on the Kubernetes cluster requires an update with new features developed by the Nautilus application development team. The existing setup includes a deployment named `nginx-deployment` and a service named `nginx-service`. Below are the necessary changes to be implemented without deleting the deployment and service:

1.) Modify the service nodeport from `30008` to `32165`

2.) Change the replicas count from `1` to `5`

3.) Update the image from `nginx:1.18` to `nginx:latest`

`Note:` The `kubectl` utility on the `jump-host` has been configured to work with the Kubernetes cluster.

---

## 🚀 Complete Solution

## Step 1: Export and Modify the Deployment

First, we export the current configuration of the deployment to a YAML file, edit it to update the replicas and image, and save the changes.

```bash
thor@jump-host ~$ kubectl get deployments/nginx-deployment -o yaml > dep.yaml
thor@jump-host ~$ nano dep.yaml

```

**Updated `dep.yaml` (Key Changes Highlighted):**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: default
  # ... (other metadata omitted for brevity)
spec:
  progressDeadlineSeconds: 600
  replicas: 5                       # <-- Updated from 1 to 5
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: nginx-app
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: nginx-app
      name: nginx-replica
    spec:
      containers:
      - image: nginx:latest         # <-- Updated from nginx:1.18 to latest
        imagePullPolicy: IfNotPresent
        name: nginx-container
# ... (remaining config omitted)

```

## Step 2: Export and Modify the Service

Next, we export the service configuration, edit the `nodePort` value, and save the file.

```bash
thor@jump-host ~$ kubectl get service/nginx-service -o yaml > svc.yaml
thor@jump-host ~$ nano svc.yaml

```

**Updated `svc.yaml` (Key Changes Highlighted):**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  namespace: default
  # ... (other metadata omitted for brevity)
spec:
  clusterIP: 10.43.207.82
  clusterIPs:
  - 10.43.207.82
  ports:
  - nodePort: 32165                 # <-- Updated from 30008 to 32165
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: nginx-app
  sessionAffinity: None
  type: NodePort
status:
  loadBalancer: {}

```

## Step 3: Apply the Changes

With both YAML files updated, apply the configurations directly to the cluster. This updates the resources in place without deleting them.

```bash
thor@jump-host ~$ kubectl apply -f dep.yaml
deployment.apps/nginx-deployment configured

thor@jump-host ~$ kubectl apply -f svc.yaml
service/nginx-service configured

```

## Step 4: Verify the Updates

Finally, check the cluster resources to ensure the new pods are running and the service port has been successfully updated.

```bash
thor@jump-host ~$ kubectl get all

```

**Verification Output:**

```text
NAME                                        READY   STATUS    RESTARTS   AGE
pod/nginx-deployment-6849884b5d-5l9pl       1/1     Running   0          33s
pod/nginx-deployment-6849884b5d-96g78       1/1     Running   0          26s
pod/nginx-deployment-6849884b5d-f2tnt       1/1     Running   0          26s
pod/nginx-deployment-6849884b5d-l68gv       1/1     Running   0          33s
pod/nginx-deployment-6849884b5d-z99wh       1/1     Running   0          33s

NAME                    TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
service/kubernetes      ClusterIP   10.43.0.1      <none>        443/TCP        74m
service/nginx-service   NodePort    10.43.207.82   <none>        80:32165/TCP   13m

NAME                               READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/nginx-deployment   5/5     5            5           13m

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/nginx-deployment-6849884b5d   5         5         5       33s
replicaset.apps/nginx-deployment-79b79679fc   0         0         0       13m

```

### ✅ Success Summary

* **Replicas:** Scaled up successfully (5/5 Pods running).
* **Image Update:** New ReplicaSet (`6849884b5d`) created and running the updated image.
* **Service Port:** Successfully updated to `32165`.