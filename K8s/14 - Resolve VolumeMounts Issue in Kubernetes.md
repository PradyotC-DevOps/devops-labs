# 14 - Resolve VolumeMounts Issue in Kubernetes

## 📋 Task Overview

We encountered an issue with our Nginx and PHP-FPM setup on the Kubernetes cluster this morning, which halted its functionality. Investigate and rectify the issue:

The pod name is `nginx-phpfpm` and configmap name is `nginx-config`. Identify and fix the problem.

Once resolved, copy `/home/thor/index.php` file from the `jump host` to the `nginx-container` within the nginx document root. After this, you should be able to access the website using the `Website` button on the top bar.

**Note:** The `kubectl` utility on the `jump-host` has been configured to work with the Kubernetes cluster.

---

## 🚀 Complete Solution

1. **Investigate the Failing Pod:**
To determine why PHP files were failing to load, inspect the configuration and the pod's volume mounts to ensure Nginx and PHP-FPM are looking at the exact same directory.

```bash
# View the Nginx configuration to find the expected document root
kubectl get configmap nginx-config -o yaml

```

*Sample Output (ConfigMap):*

```text
    # Set nginx to serve files from the shared volume!
    root /var/www/html;
    index  index.html index.htm index.php;

```

```bash
# Check the pod's volume mounts
kubectl describe pod nginx-phpfpm

```

*Sample Output (Pod Mounts):*

```text
  php-fpm-container:
    Mounts:
      /usr/share/nginx/html from shared-files (rw)
  nginx-container:
    Mounts:
      /var/www/html from shared-files (rw)

```

*Diagnosis:* The output clearly reveals a path mismatch. The `nginx-container` and the `nginx-config` were using `/var/www/html`, but the `php-fpm-container` was mistakenly mounting the shared volume to `/usr/share/nginx/html`.


2. **Extract and Fix the Pod Manifest:**
Since volume mounts cannot be modified on a live pod, export the existing pod definition and correct the incorrect mount path.

```bash
# Export the pod configuration
kubectl get pod nginx-phpfpm -o yaml > pod.yaml

# Automatically replace the incorrect path in the file
sed -i 's|/usr/share/nginx/html|/var/www/html|g' pod.yaml

```


3. **Recreate the Pod:**
Use the `replace --force` command to safely tear down the broken pod and immediately recreate it with the fixed volume mounts.

```bash
kubectl replace --force -f pod.yaml

```

*Sample Output:*

```text
pod "nginx-phpfpm" deleted
pod/nginx-phpfpm replaced

```

Wait a moment and confirm both containers are Running:

```bash
kubectl get pods

```

*Sample Output:*

```text
NAME           READY   STATUS    RESTARTS   AGE
nginx-phpfpm   2/2     Running   0          12s

```


4. **Deploy the Application Code:**
With the pod functional, copy the `index.php` file from the jump-host into the now-corrected document root within the Nginx container.

```bash
kubectl cp /home/thor/index.php nginx-phpfpm:/var/www/html/index.php -c nginx-container

```


5. **Verify the Resolution:**
Test the application locally via the NodePort to ensure PHP is correctly rendering the dynamic `phpinfo()` page instead of throwing a 404 or File Not Found error.

```bash
# Check for HTTP 200 OK
curl -w "%{http_code}\n\n" -o /dev/null -s "http://localhost:30008"

```

*Sample Output:*

```text
200

```

```bash
# Preview the rendered HTML
curl -s http://localhost:30008 | head -10

```