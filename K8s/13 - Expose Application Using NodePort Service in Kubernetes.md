# 13 - Expose Application Using NodePort Service in Kubernetes

## 📋 Task Overview

The Nautilus DevOps team has already deployed a `ReplicaSet` to host an application that requires a highly available infrastructure. Your task is to expose the application running in the existing `ReplicaSet` by creating a Kubernetes `NodePort` Service.

Follow the specifications below to create the Service and ensure the application pods are accessible:

1. A ReplicaSet named `nginx-replicaset` is already running in the cluster.
2. The pods managed by the ReplicaSet use the following labels: Assign labels `app` as `nginx_app`, and `type` as `front-end`.
3. Create a NodePort Service named `nginx-service` to expose the application.
4. Set the NodePort to `30080`.
5. Expose port `80` of the application.

**Note:** Do not delete or modify the configuration of the deployed `ReplicaSet` application.

---

## 🚀 Complete Solution

1. **Inspect the Existing ReplicaSet:**
Before creating the Service, determine the specific labels used by the target application pods. This ensures the Service correctly routes incoming traffic to the deployed containers.

```bash
kubectl get replicaset.apps/nginx-replicaset -o yaml > rep.yaml
cat rep.yaml

```

*Result: The inspection revealed that the pods require a dual-label selector matching `app: nginx_app` and `type: front-end`.*


2. **Create the Service Manifest:** Utilize a HereDoc for precise configuration formatting.
Instead of wrestling with the limited `kubectl create service nodeport` imperative flags, generate a precise, declarative YAML manifest that correctly defines the exact NodePort constraint and necessary selectors.

```bash
cat <<EOF > svc.yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx_app
    type: front-end
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30080
EOF

```


3. **Apply the Configuration:**
Deploy the explicitly defined Service directly into the Kubernetes cluster.

```bash
kubectl apply -f svc.yaml

```


4. **Verify the Service and Endpoints:** Ensure all three replicas are successfully receiving traffic.
Validate the deployment by querying the dynamically generated endpoints.

```bash
kubectl get endpoints nginx-service

```

*Result:* The command outputs `10.22.0.10:80,10.22.0.11:80,10.22.0.9:80`, proving that the Service dynamically mapped all three active pods, completing the requirements.