## 1.

The Nautilus DevOps team is actively engaged in practicing pods and services deployment on the Kubernetes platform, preparing for the migration of numerous applications to this environment. Recently, a team member has been assigned the task of creating a pod with specific requirements:

a. Create a pod named pod-httpd-t1q1 utilizing the httpd image, ensuring the use of the latest tag, denoted as httpd:latest (remember to mention tag name while defining the image).
b. Set the app label to httpd_app_t1q1, and name the container as httpd-container-t1q1.
This task aims to establish a new pod adhering to defined image specifications and container naming conventions.
Note: The kubectl utility on jump-host has been configured to work with the kubernetes cluster.


---

### Execution Steps

**1. Generate the Base Manifest**
Use `kubectl run` with the `--dry-run=client` flag to generate the initial YAML configuration without applying it to the cluster immediately. This allows for manual edits to meet requirements that cannot be passed directly via command-line flags.

```bash
kubectl run pod-httpd-t1q1 \
  --image=httpd:latest \
  --labels="app=httpd_app_t1q1" \
  --dry-run=client \
  -o yaml > pod.yaml

```

**2. Modify the Container Name**
By default, `kubectl run` sets the container name to match the Pod name. To meet the specific container naming requirement, edit the generated `pod.yaml` file.

```bash
nano pod.yaml

```

*Update the `name` field under `spec.containers`:*

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: httpd_app_t1q1
  name: pod-httpd-t1q1
spec:
  containers:
  - image: httpd:latest
    name: httpd-container-t1q1    # <--- Updated from pod-httpd-t1q1

```

**3. Apply the Manifest**
Deploy the configured Pod to the Kubernetes cluster.

```bash
kubectl apply -f pod.yaml

```

*Expected Output:* `pod/pod-httpd-t1q1 created`

**4. Verification**
Verify that the Pod is running and inspect its configuration to confirm all requirements are met.

```bash
kubectl get pods
kubectl describe pod pod-httpd-t1q1

```

*Verification Checklist:*

* [x] Status is `Running`
* [x] Label `app=httpd_app_t1q1` is present under `Labels`
* [x] Container name under `Containers:` is `httpd-container-t1q1`
* [x] Image is `httpd:latest`

### Troubleshooting Notes

* **Invalid Argument Error:** When using `kubectl run`, ensure no extra strings are placed before the pod name (e.g., `kubectl run pod <name>`), as Kubernetes will interpret the string as the pod name and the actual intended name as an argument for the container.
* **Recreating Pods:** If a Pod is deployed with an incorrect configuration, it must be deleted (`kubectl delete pod <pod-name>`) before applying the corrected YAML file, as certain fields (like container names) are immutable once the Pod is created.

## 2.

The Nautilus application development team aims to test a Pod creation by creating a httpd based Pod on the Kubernetes cluster. The specifications for the same are as follows:


Create a Pod named httpd-test-t1q4 using httpd:alpine3.19 image, it must have a label app: httpd

---

### Execution Steps

**1. Generate the Manifest**
Generate the Pod configuration using a dry-run. Storing this as a YAML file is a best practice for documentation and Infrastructure as Code (IaC).

```bash
kubectl run httpd-test-t1q4 \
  --image="httpd:alpine3.19" \
  --labels="app=httpd" \
  --dry-run=client \
  -o yaml > pod2.yaml

```

**2. Apply the Manifest**
Deploy the Pod to the cluster using the generated YAML file.

```bash
kubectl apply -f pod2.yaml

```

*Expected Output:* `pod/httpd-test-t1q4 created`

**3. Verification**
Inspect the Pod to ensure it is running and meets all requested specifications.

```bash
kubectl describe pod/httpd-test-t1q4

```

*Verification Checklist:*

* [x] **Status:** `Running`
* [x] **Labels:** `app=httpd`
* [x] **Image:** `httpd:alpine3.19` (Successfully pulled in ~1.8s)
* [x] **Events:** No scheduling or image pull errors.

---

### Pro-Tip / Advice

Because this specific task did not require any manual YAML modifications (like overriding the auto-generated container name), you could have successfully deployed it using a single imperative command without creating the YAML file:

```bash
kubectl run httpd-test-t1q4 --image="httpd:alpine3.19" --labels="app=httpd"

```

However, your approach of generating the `pod2.yaml` file first is heavily encouraged in real-world DevOps environments, as it allows you to commit the manifest to a Git repository for version control before applying it.

-----------------

## 3

The Nautilus devops team found that one of the applications that is deployed on the cluster is having some performance issues, they want to make some changes so that it can handle some more traffic. As per new updates some new changes need to be made in this existing setup. So update the deployment as per details mentioned below:


The deployment name is blue-app-t2q5, change its replicas count from 1 to 3.

Note: The kubectl utility on jump-host has been configured to work with the kubernetes cluster.

---

### Execution Steps

**1. Verify Current State**
Check the existing resources to confirm the deployment name and its current replica count.

```bash
kubectl get deployment blue-app-t2q5

```

*(Pre-scaling state showed 1/1 ready replicas).*

**2. Scale the Deployment**
Use the imperative `scale` command to update the replica count to 3.

```bash
kubectl scale deployment.apps/blue-app-t2q5 --replicas=3

```

*Expected Output:* `deployment.apps/blue-app-t2q5 scaled`

**3. Verification**
Verify that the ReplicaSet has been updated and the new Pods have successfully spun up and reached the `Running` state.

```bash
kubectl get all

```

*Verification Checklist:*

* [x] **Deployment:** `deployment.apps/blue-app-t2q5` shows `3/3` READY.
* [x] **ReplicaSet:** `replicaset.apps/blue-app-t2q5-<hash>` shows DESIRED `3` and CURRENT `3`.
* [x] **Pods:** Three distinct pods starting with `blue-app-t2q5-` are in the `Running` state.

---

### Pro-Tip / Advice

Using `kubectl scale` is the fastest way to handle a sudden surge in traffic (imperative approach). However, if this cluster is managed using **Infrastructure as Code (IaC)**, running this command creates a drift between your cluster's actual state and your source code.

To make this change permanent in a declarative workflow, you should also update the deployment's underlying YAML file:

1. Update the manifest manually: `kubectl edit deployment blue-app-t2q5` (change `replicas: 1` to `replicas: 3`).
2. Or, update your source Git repository and re-apply: `kubectl apply -f deployment.yaml`.

---------------------------------------------------------------

## 4

The Nautilus DevOps team has started practicing some pods and services deployment on Kubernetes platform, as they are planning to migrate most of their applications on Kubernetes. Recently one of the team members has been assigned a task to create a deployment as per details mentioned below:


Create a deployment named httpd-t2q1 to deploy the application httpd-t2q1 using the image httpd:latest (remember to mention the tag as well)

Note: The kubectl utility on jump-host has been configured to work with the kubernetes cluster.

---

### Execution Steps

**1. Create the Deployment**
Use the imperative `create deployment` command to instantly generate the deployment and its underlying ReplicaSet and Pods.

```bash
kubectl create deployment httpd-t2q1 --image=httpd:latest

```

*Expected Output:* `deployment.apps/httpd-t2q1 created`

**2. Verify the Resources**
Confirm that the Deployment, ReplicaSet, and Pod have been successfully created and that the Pod has reached the `Running` state.

```bash
# Check the deployment status
kubectl get deployments

# Check the running pods
kubectl get pods

# Comprehensive check of all resources
kubectl get all

```

*Verification Checklist:*

* [x] **Deployment:** `deployment.apps/httpd-t2q1` shows `1/1` READY.
* [x] **ReplicaSet:** `replicaset.apps/httpd-t2q1-<hash>` shows DESIRED `1` and CURRENT `1`.
* [x] **Pod:** `pod/httpd-t2q1-<hash>-<hash>` is in the `Running` state.

---

### Pro-Tip / Advice

While the imperative `kubectl create deployment` command is perfect for quick tasks and exams, you can always combine it with the `--dry-run=client -o yaml` flags (just like you did in the first task) if you ever need to generate a base YAML template for a deployment.

```bash
# Example for generating a YAML template for GitOps / IaC:
kubectl create deployment httpd-t2q1 --image=httpd:latest --dry-run=client -o yaml > deployment.yaml

```

-----------------------------------------------

## 5

The Nautilus DevOps team plans to deploy applications on a Kubernetes cluster for the migration of some existing applications. Recently, a team member has been tasked with creating below components:


a. Create a ReplicaSet named httpd-replicaset-t3q4 using httpd image with latest tag only (remember to mention tag i.e httpd:latest).

b. Label app should be httpd_app_t3q4, label type should be front-end-t3q4.

c. The container should be named as httpd-container-t3q4, also make sure replicas counts are 4.

Note: The kubectl utility on jump-host has been configured to work with the kubernetes cluster.

---

### Execution Steps

**1. Create the ReplicaSet Manifest**
Because there is no native command to auto-generate a ReplicaSet, manually create the `rs.yaml` file defining the replicas, the label selector, and the pod template.

```bash
nano rs.yaml

```

*Contents of `rs.yaml`:*

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: httpd-replicaset-t3q4
  labels:
    app: httpd_app_t3q4
    type: front-end-t3q4
spec:
  replicas: 4
  selector:
    matchLabels:
      app: httpd_app_t3q4
      type: front-end-t3q4
  template:
    metadata:
      labels:
        app: httpd_app_t3q4
        type: front-end-t3q4
    spec:
      containers:
      - name: httpd-container-t3q4
        image: httpd:latest

```

**2. Apply the Manifest**
Deploy the ReplicaSet to the cluster.

```bash
kubectl apply -f rs.yaml

```

*Expected Output:* `replicaset.apps/httpd-replicaset-t3q4 created`

**3. Verification**
Confirm that the ReplicaSet is managing the correct number of pods and that the generated pods have the required labels applied to them.

```bash
# Check the ReplicaSet status
kubectl get rs httpd-replicaset-t3q4

# Verify the pods and their labels
kubectl get pods --show-labels -l app=httpd_app_t3q4

```

*Verification Checklist:*

* [x] **ReplicaSet:** Shows DESIRED `4` and CURRENT `4`.
* [x] **Pods:** Four distinct pods are `Running`.
* [x] **Pod Labels:** Ensure `app=httpd_app_t3q4` and `type=front-end-t3q4` are present on all four pods.

---

### Pro-Tip / Advice

If you are ever in an exam setting (like the CKA or CKAD) and need to create a `ReplicaSet` quickly without memorizing the exact YAML structure, you can generate a `Deployment` YAML using a dry-run, output it to a file, and simply change `kind: Deployment` to `kind: ReplicaSet`.

```bash
# Quick trick to generate the skeleton:
kubectl create deployment temp-deployment --image=httpd:latest --dry-run=client -o yaml > rs.yaml

```

Once generated, you can open the file, strip out the deployment-specific fields (like `strategy`), change the `kind`, update your labels, and apply it!

----------------------------------------------

## 6

The Nautilus DevOps team is actively creating jobs within the Kubernetes cluster. While they are in the process of finalizing actual scripts/commands, they are presently structuring templates and testing the jobs using placeholder commands. Below are the specifications for creating a job template:


Create a job named countdown-nautilus-t3q2.

The spec template should be named as countdown-nautilus-t3q2 (under metadata), and the container should be named as container-countdown-nautilus-t3q2.

Use image debian with latest tag only and remember to mention tag i.e debian:latest, and restart policy should be Never.

Use command sleep 5.

Note: The kubectl utility on jump-host has been configured to work with the kubernetes cluster.

---

### Execution Steps

**1. Create the Job Manifest**
Create a new file named `job.yaml` and define the Job specifications.

```bash
nano job.yaml

```

*Paste the following YAML configuration:*

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: countdown-nautilus-t3q2
spec:
  template:
    metadata:
      name: countdown-nautilus-t3q2
    spec:
      containers:
      - name: container-countdown-nautilus-t3q2
        image: debian:latest
        command: ["sleep", "5"]
      restartPolicy: Never

```

**2. Apply the Manifest**
Deploy the Job to the cluster.

```bash
kubectl apply -f job.yaml

```

*Expected Output:* `job.batch/countdown-nautilus-t3q2 created`

**3. Verification**
Verify that the Job was created successfully and monitor the pod as it executes the `sleep 5` command and transitions to the `Completed` state.

```bash
# Check the Job status
kubectl get jobs

# Watch the Pod status (it should take ~5 seconds to complete)
kubectl get pods -w

# Inspect the Job details to confirm names and commands
kubectl describe job countdown-nautilus-t3q2

```

*Verification Checklist:*

* [x] **Job:** Status shows `1/1` completions (after 5 seconds).
* [x] **Pod:** Status transitions from `Running` to `Completed`.
* [x] **Container Name:** `container-countdown-nautilus-t3q2` is verified in the describe output.

---

### Pro-Tip / Advice

If you want to generate the base skeleton for a Job quickly via the command line instead of writing it entirely from scratch, you can use:

```bash
kubectl create job countdown-nautilus-t3q2 --image=debian:latest --dry-run=client -o yaml -- sleep 5 > job.yaml

```

----------------------

## 7

A DevOps engineer attempted to deploy a Python application on the Kubernetes cluster. Unfortunately, due to misconfigurations, the application failed to launch. Your task is to investigate and rectify the issues.


Deployment and Service Details:

Deployment Name: python-deployment-nautilus-t4q4
Image Used: poroko/flask-demo-app
Ensure that the deployment and service configurations for this app are correctly deployed. Configure the nodePort to be 32345, and set the targetPort to match the default port of the Python Flask application.

Your objective is to identify and correct any misconfigurations affecting the deployment and service of the Python application. Once rectified, verify the application's accessibility through the App button.

Note: The kubectl on jump-host has been configured to work with the kubernetes cluster.

---

### Execution Steps

**1. Diagnose the State**
First, inspect the cluster to identify failing pods and check the current deployment configurations.

```bash
# Identify failing pods
kubectl get pods

# Inspect deployment to check image name and pod labels
kubectl describe deployment python-deployment-nautilus-t4q4

```

*Finding:* The pod label is `app=python_app_t4q4` and the image is incorrectly set to `poroko/flask-app-demo`.

**2. Patch the Deployment**
Use the imperative `set image` command to instantly update the container image to the correct one without needing to edit the YAML manually.

```bash
kubectl set image deployment/python-deployment-nautilus-t4q4 python-container-nautilus=poroko/flask-demo-app

```

*Expected Output:* `deployment.apps/python-deployment-nautilus-t4q4 image updated`

**3. Configure and Apply the Service**
Create a new `Service` manifest to map the required NodePort (`32345`) to the default Flask application target port (`5000`).

```bash
nano service.yaml

```

*Contents of `service.yaml`:*

```yaml
apiVersion: v1
kind: Service
metadata:
  name: python-service-nautilus-t4q4
spec:
  type: NodePort
  selector:
    app: python_app_t4q4
  ports:
    - port: 5000
      targetPort: 5000
      nodePort: 32345

```

```bash
kubectl apply -f service.yaml

```

*Expected Output:* `service/python-service-nautilus-t4q4 configured` (or `created`)

**4. Final Verification**
Verify that the new pod is running cleanly and that the service has properly mapped the ports.

```bash
kubectl get all

```

*Verification Checklist:*

* [x] **Pod Status:** The pod is now in the `Running` state (the old `ImagePullBackOff` pod was terminated).
* [x] **Service Type:** `python-service-nautilus-t4q4` is configured as `NodePort`.
* [x] **Port Mapping:** The service shows `5000:32345/TCP`.

---

### Pro-Tip / Advice

While creating the YAML for the service is perfectly fine and often preferred for documentation, you could have bypassed writing `service.yaml` entirely by using the imperative `kubectl expose` command, which automatically pulls the selectors from the deployment:

```bash
# This creates the service, but since you can't specify nodePort directly here...
kubectl expose deployment python-deployment-nautilus-t4q4 --name=python-service-nautilus-t4q4 --type=NodePort --port=5000 --target-port=5000

# ...you would then edit it to enforce the specific nodePort 32345:
kubectl edit svc python-service-nautilus-t4q4

```

--------------------------------------------

## 8

Despite diligent efforts, the DevOps team encountered difficulties deploying the orange-app-deployment-t4q6 on the Kubernetes cluster. Regrettably, this app is currently inaccessible. Your prompt attention to resolving this issue is crucial.


The orange-app-deployment-t4q6 is currently facing accessibility issues on the Kubernetes cluster. Your immediate objective is to investigate and rectify this issue to restore the app's functionality and accessibility.

Please prioritize diagnosing the root cause of the deployment issue and apply necessary fixes to ensure the successful deployment and accessibility of the orange-app-deployment-t4q6.

Note: The kubectl on jump-host has been configured to work with the kubernetes cluster.

---

### Execution Steps

**1. Diagnose Deployment and Pod State**
Verify the status of the deployment and its pods to rule out container or image issues.

```bash
kubectl describe deployments.apps/orange-app-deployment-t4q6

```

*Finding:* The deployment showed `1 available` pod, and events confirmed the Nginx container started successfully. The pod's label was correctly defined as `app=orange-app-t4q6`.

**2. Diagnose Service State**
Inspect the existing services and their specific configurations to identify routing failures.

```bash
# List all services
kubectl get svc

# Inspect the specific orange-app service to check its selector
kubectl get svc/orange-app-service-t4q6 -o wide

```

*Finding:* The `SELECTOR` column showed `app=orage-app-t4q6` (missing the "n" in orange). Traffic sent to port `31112` failed because the service was routing to non-existent pods.

**3. Apply the Fix**
Use the imperative `set selector` command to instantly update the service's selector to match the deployment's labels.

```bash
kubectl set selector service orange-app-service-t4q6 app=orange-app-t4q6

```

*Expected Output:* `service/orange-app-service-t4q6 selector updated`

**4. Final Verification**
Test the NodePort directly on the jump-host to verify the application is now accessible.

```bash
curl http://localhost:31112

```

*Verification Checklist:*

* [x] **Service Selector:** Corrected to `app=orange-app-t4q6`.
* [x] **Application Response:** `curl` returns the standard `Welcome to nginx!` HTML page.

---

### Pro-Tip / Advice

When a Service isn't routing traffic to your pods, checking the `Endpoints` is often the fastest way to confirm a selector mismatch.

```bash
kubectl get endpoints orange-app-service-t4q6

```

Before your fix, this command would have shown `<none>` under the `ENDPOINTS` column, immediately confirming that the Service was failing to select any backend pods. After the fix, it would display the internal IP of your Nginx pod.

------------------------------------------

## 9

An application was previously deployed on the Kubernetes cluster, the deployment name is deployment-t5q2. This application is used by another applications within the same Kubernetes cluster. To enable access to this app, we require the creation of a ClusterIP service for the same.


Create a service named deployment-svc-t5q2. It must be a ClusterIP service which should use port 8090 and target port should be 80.

---

### Execution Steps

**1. Expose the Deployment**
Use the imperative `expose` command to generate the service directly from the existing deployment, ensuring the selectors are mapped automatically.

```bash
kubectl expose deployment deployment-t5q2 \
  --name=deployment-svc-t5q2 \
  --type=ClusterIP \
  --port=8090 \
  --target-port=80

```

*Expected Output:* `service/deployment-svc-t5q2 exposed`

**2. Verify Configuration and Endpoints**
Confirm the service was created with the correct ports and is tracking the backend pod IP correctly.

```bash
kubectl get svc deployment-svc-t5q2
kubectl get endpoints deployment-svc-t5q2

```

*Verification Check:* The service was successfully assigned ClusterIP `10.43.111.80`, and the endpoints correctly identified the pod IP `10.22.0.26:80`.

**3. Internal Connectivity Test**
Because `ClusterIP` services are not exposed to the external network or the host machine (`localhost`), they must be tested from within the cluster. Launch an ephemeral debug pod to query the service via its internal DNS name.

```bash
kubectl run -i --tty --rm debug-pod --image=busybox --restart=Never -- wget -qO- http://deployment-svc-t5q2:8090

```

*Verification Check:* The debug pod successfully resolved the service name, connected to port 8090, and returned the default Nginx welcome page before automatically deleting itself.

--------------------

## 10

During an investigating it was found that one of the applications on the Kubernetes cluster is having some issues, the team discovered that the service was configured with an incorrect target port. We need to update the service as follows:


Update service-t5q4 service to use target port 80.

---

### Execution Steps

**1. Diagnose the Mismatch**
First, inspect both the service and the deployment to verify the exact ports they are configured to use.

```bash
# Check the service's targetPort
kubectl get svc service-t5q4 -o yaml | grep -i targetPort

# Check the deployment's containerPort
kubectl get deployment deployment-t5q4 -o yaml | grep -i containerPort

```

*Finding:* The command output confirmed the Service was looking for port `81`, but the Deployment container was listening on port `80`.

**2. Patch the Service Configuration**
Use the imperative `edit` command to instantly modify the service object running in the cluster.

```bash
kubectl edit svc service-t5q4

```

*Action:* Under the `spec.ports` section, changed `targetPort: 81` to `targetPort: 80`. Saved and exited the text editor.

*Expected Output:* `service/service-t5q4 edited`

**3. Final Verification**
Test the connection locally to ensure the port mapping is now successfully routing traffic from the NodePort all the way to the Nginx container.

```bash
# Double check the updated mapping
kubectl get svc service-t5q4 -o wide

# Test the connection to the application
curl http://localhost:30091

```

*Verification Checklist:*

* [x] **Target Port:** Corrected to `80`.
* [x] **Application Response:** `curl` successfully returns the "Welcome to nginx!" HTML page, confirming end-to-end connectivity.

---