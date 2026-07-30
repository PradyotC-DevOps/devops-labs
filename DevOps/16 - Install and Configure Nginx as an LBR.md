# 16: Install and Configure Nginx as an LBR

## 📋 Task Overview

<div class="markdown-body text-base">


<meta charset="utf-8">


<p>Day by day traffic is increasing on one of the websites managed by the <code>Nautilus</code> production support team. Therefore, the team has observed a degradation in website performance. Following discussions about this issue, the team has decided to deploy this application on a high availability stack i.e on <code>Nautilus</code> infra in <code>Stratos DC</code>. They started the migration last month and it is almost done, as only the LBR server configuration is pending. Configure LBR server as per the information given below:<br><br></p>

</div><br><div class="markdown-body text-sm mb-8">


<meta charset="utf-8">


<p>a. Install <code>nginx</code> on the <code>LBR</code> (load balancer) server if it is not already installed.<br><br></p>
<p>b. Configure load-balancing with the <code>http</code> context making use of all <code>App Servers</code>. Ensure that you update only the main Nginx configuration file located at <code>/etc/nginx/nginx.conf</code>.<br><br></p>
<p>c. Make sure you do not update the apache port that is already defined in the apache configuration on all app servers, also make sure apache service is up and running on all the app servers.<br><br></p>
<p>d. Once done, you can access the website by running <code>curl http://stlb01:80</code> in the terminal.</p>

</div>

---

## 🚀 Complete Solution

1. **Verify and Enable App Servers:**
* SSH'd into each application server (`stapp01`, `stapp02`, `stapp03`).
* Discovered that the Apache HTTP server (`httpd`) was pre-configured to listen on non-standard ports (e.g., `5003`).
* Strictly adhering to the requirements, the port configurations were left untouched. The service was simply enabled and started across all nodes:
```bash
sudo systemctl start httpd
sudo systemctl enable httpd

```




2. **Install and Enable Nginx on the LBR:**
* SSH'd into the load balancer node (`stlb01`).
* Checked the status of Nginx, enabled the service to persist across reboots, and started it:
```bash
sudo systemctl enable nginx
sudo systemctl start nginx

```




3. **Configure the Load Balancer (`http` context):**
* Edited the main Nginx configuration file (`/etc/nginx/nginx.conf`).
* Defined an `upstream` block within the `http` context containing the hostnames and specific pre-configured ports of the backend application servers.
* Updated the `server` block to proxy incoming requests on port 80 to the upstream group.
```nginx
http {
    ...
    upstream backend_apps {
        server stapp01:5003;
        server stapp02:5003;
        server stapp03:5003;
    }

    server {
        listen       80;
        listen       [::]:80;
        server_name  _;

        location / {
            proxy_pass http://backend_apps;
        }
    }
    ...
}

```


* Validated the syntax using `sudo nginx -t` and reloaded the service using `sudo systemctl reload nginx`.


4. **Validation:**
* Returned to the jump host and ran `curl -IsS http://stlb01:80` multiple times.
* Confirmed a `200 OK` response each time, verifying that the LBR was successfully distributing traffic to the backend Apache servers.



**💡 Key Learnings & Gotchas:**

* **Preserving Backend Architectures:** A common trap in load-balancer configurations is forcing backend servers to conform to port `80`. Leaving the backend applications on their isolated, pre-defined ports (like `5003`) and handling the port translation entirely via Nginx's `proxy_pass` directive is a safer, more robust architectural practice.