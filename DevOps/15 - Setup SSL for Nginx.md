# 15 - Setup SSL for Nginx

## 📋 Task Overview

<div class="flex flex-col"><!----><div class="markdown-body text-base">


<p>The system admins team of <code>xFusionCorp Industries</code> needs to deploy a new application on <code>App Server 2</code> in <code>Stratos Datacenter</code>. They have some pre-requites to get ready that server for application deployment. Prepare the server as per requirements shared below:<br><br></p>

</div><br><div class="markdown-body text-sm mb-8">

<p>1. Install and configure <code>nginx</code> on <code>App Server 2</code>.<br><br></p>
<p>2. On <code>App Server 2</code> there is a self signed SSL certificate and key present at location <code>/tmp/nautilus.crt</code> and <code>/tmp/nautilus.key</code>. Move them to some appropriate location and deploy the same in Nginx.<br><br></p>
<p>3. Create an <code>index.html</code> file with content <code>Welcome!</code> under Nginx document root.<br><br></p>
<p>4. For final testing try to access the <code>App Server 2</code> link (via hostname) from <code>jump host</code> using curl command. For example: <code>curl -Ik https://&lt;app-server-name&gt;/</code>.</p>

---

## 🚀 Complete Solution

### 1. SSH into App Server 2
First, connect to `App Server 2` from the jump host. (Standard Nautilus credentials for App Server 2: user `steve`, password `Am3ric@`).

```bash
ssh steve@stapp02

```

Once logged in, switch to the `root` user to execute the installation and configuration commands seamlessly:

```bash
sudo su -

```

### 2. Install Nginx

Install the EPEL repository (required on CentOS/RHEL systems for Nginx) and install Nginx.

```bash
yum install epel-release -y
yum install nginx -y

```

### 3. Move and Secure the SSL Certificates

Create a dedicated directory for the Nginx SSL certificates, then move the existing files from `/tmp`.

```bash
mkdir -p /etc/nginx/ssl
mv /tmp/nautilus.crt /etc/nginx/ssl/
mv /tmp/nautilus.key /etc/nginx/ssl/

# Ensure strict permissions on the private key
chmod 600 /etc/nginx/ssl/nautilus.key

```

### 4. Configure Nginx for SSL

Create a new configuration file in the `/etc/nginx/conf.d/` directory to define an HTTPS server block.

```bash
cat <<EOF> /etc/nginx/conf.d/ssl.conf
server {
    listen 443 ssl;
    server_name stapp02;

    ssl_certificate /etc/nginx/ssl/nautilus.crt;
    ssl_certificate_key /etc/nginx/ssl/nautilus.key;

    root /usr/share/nginx/html;
    index index.html;

    location / {
    }
}
EOF

```

### 5. Create the Index File

Create the requested `index.html` file containing the text `Welcome!` within the default Nginx document root.

```bash
echo 'Welcome!' > /usr/share/nginx/html/index.html

```

### 6. Test and Start Nginx

Verify the Nginx configuration syntax is correct, then start and enable the service to persist across reboots.

```bash
nginx -t
systemctl start nginx
systemctl enable nginx

```

**Expected Output:**

```text
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
Created symlink /etc/systemd/system/multi-user.target.wants/nginx.service → /usr/lib/systemd/system/nginx.service.

```

### 7. Verify the Configuration from the Jump Host

Exit the root session and the SSH session to return to the `jump host`.

```bash
exit  # Exit root shell
exit  # Exit SSH session

```

Run the `curl` command using the `-I` (fetch headers only) and `-k` (insecure/allow self-signed certs) flags to verify that SSL is working and Nginx is serving traffic securely.

```bash
curl -Ik https://stapp02/

```

**Expected Output:**

```http
HTTP/1.1 200 OK
Server: nginx/1.20.1
Date: Sat, 25 Jul 2026 13:45:00 GMT
Content-Type: text/html
Content-Length: 9
Last-Modified: Sat, 25 Jul 2026 13:43:00 GMT
Connection: keep-alive
ETag: "62e1a3bc-9"
Accept-Ranges: bytes

```

To verify the content of the page, run `curl` without the `-I` flag:

```bash
curl -k https://stapp02/

```

**Expected Output:**

```text
Welcome!

```

## ✅ Conclusion

Successfully installed Nginx, applied self-signed SSL certificates, configured an HTTPS server block on port 443, deployed the application code, and validated the secure connection from the jump host.