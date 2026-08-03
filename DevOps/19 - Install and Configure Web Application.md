# 19 - Install and Configure Web Application

## 📋 Task Overview

<div class="flex flex-col"><!----><div class="markdown-body text-base">


<meta charset="utf-8">


<p>xFusionCorp Industries is planning to host two static websites on their infra in <code>Stratos Datacenter</code>. The development of these websites is still in-progress, but we want to get the servers ready. Please perform the following steps to accomplish the task:</p>

</div><br><div class="markdown-body text-sm mb-8">


<meta charset="utf-8">


<p>a. Install <code>httpd</code> package and dependencies on <code>app server 3</code>.</p>
<p>b. Apache should serve on port <code>6400</code>.</p>
<p>c. There are two website's backups <code>/home/thor/beta</code> and <code>/home/thor/games</code> on <code>jump_host</code>. Set them up on Apache in a way that <code>beta</code> should work on the link <code>http://localhost:6400/beta/</code> and <code>games</code> should work on link <code>http://localhost:6400/games/</code> on the mentioned app server. </p>
<p>d. Once configured you should be able to access the website using <code>curl</code> command on the respective app server, i.e <code>curl http://localhost:6400/beta/</code> and <code>curl http://localhost:6400/games/</code></p>
</div>

---

## 🚀 Complete Solution

1. **Transfer Website Backups:**
* Used `scp` (Secure Copy Protocol) from the `jump_host` to securely transfer the `beta` and `games` directories to a temporary location (`/tmp`) on `stapp03`.
```bash
scp -r /home/thor/beta /home/thor/games banner@stapp03:/tmp/

```




2. **Connect to Application Server 3:**
* SSH'd into the designated application node (`stapp03`) using the `banner` credentials.


3. **Install Apache HTTP Server:**
* Installed the `httpd` package and its dependencies using the `yum` package manager.
```bash
sudo yum install -y httpd

```




4. **Configure Apache to Listen on Port 6400:**
* Modified the main Apache configuration file (`/etc/httpd/conf/httpd.conf`).
* Located the default `Listen 80` directive and updated it to `Listen 6400`.
```bash
sudo sed -i 's/Listen 80/Listen 6400/g' /etc/httpd/conf/httpd.conf

```




5. **Deploy the Web Files:**
* Moved the `beta` and `games` directories from `/tmp/` into the default Apache document root (`/var/www/html/`). Because Apache automatically serves subdirectories as URL paths, placing them here inherently satisfies the `/beta/` and `/games/` URL requirements.
```bash
sudo mv /tmp/beta /var/www/html/
sudo mv /tmp/games /var/www/html/

```




6. **Start and Enable the Service:**
* Started the Apache service and enabled it to persist across reboots.
```bash
sudo systemctl enable --now httpd

```




7. **Validation:**
* Used the `curl` command locally on the server to verify that both endpoints returned successful HTTP responses on the custom port `6400`.



**💡 Key Learnings & Gotchas:**

* **File Transfer Permissions:** You cannot `scp` files directly into `/var/www/html/` as a standard user because that directory is owned by `root`. The standard Linux workflow is to `scp` the files to your user's home directory or `/tmp/`, SSH into the server, and then use `sudo mv` to place them in the restricted web directory.
* **Apache URL Mapping:** You don't need complex `Alias` or `VirtualHost` configurations to serve sites on paths like `/beta/`. Apache's default behavior automatically maps the physical directory `/var/www/html/beta/` to the web URL `http://<ip>:<port>/beta/`.
* **Port Configuration:** The `Listen` directive in `/etc/httpd/conf/httpd.conf` tells the web server which TCP port to bind to. Changing this requires restarting the `httpd` daemon for the changes to take effect.

### 🖥️ Proof of Execution

Below is the terminal trace demonstrating the secure copy of the files, the installation of Apache, the inline configuration of the TCP port, and the successful `curl` tests.

```bash
thor@jump-host ~$ scp -r /home/thor/beta /home/thor/games banner@stapp03:/tmp/
The authenticity of host 'stapp03 (10.244.234.200)' can't be established.
ED25519 key fingerprint is SHA256:YVvmwgg3swPcPRJ2BTNncMXJUyIns1CfJfzpkV+bL/A.
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'stapp03' (ED25519) to the list of known hosts.
banner@stapp03's password: 
index.html                  100%  117   367.0KB/s   00:00    
index.html                  100%  118   397.0KB/s   00:00    

thor@jump-host ~$ ssh banner@stapp03
banner@stapp03's password: 
[banner@stapp03 ~]$ sudo yum install -y httpd
[sudo] password for banner: 
# [...] (Dependencies resolving and installation output omitted for brevity)
Installed:
  apr-1.7.0-12.el9.x86_64                apr-util-1.6.1-23.el9.x86_64          
  apr-util-bdb-1.6.1-23.el9.x86_64       apr-util-openssl-1.6.1-23.el9.x86_64  
  centos-logos-httpd-90.9-1.el9.noarch   httpd-2.4.62-14.el9.x86_64            
  httpd-core-2.4.62-14.el9.x86_64        httpd-filesystem-2.4.62-14.el9.noarch 
  httpd-tools-2.4.62-14.el9.x86_64       mailcap-2.1.49-5.el9.noarch           
  mod_http2-2.0.26-6.el9.x86_64          mod_lua-2.4.62-14.el9.x86_64          

Complete!

[banner@stapp03 ~]$ sudo sed -i 's/Listen 80/Listen 6400/g' /etc/httpd/conf/httpd.conf

[banner@stapp03 ~]$ sudo mv /tmp/beta /var/www/html/
[banner@stapp03 ~]$ sudo mv /tmp/games /var/www/html/

[banner@stapp03 ~]$ sudo systemctl enable --now httpd
Created symlink /etc/systemd/system/multi-user.target.wants/httpd.service → /usr/lib/systemd/system/httpd.service.

[banner@stapp03 ~]$ curl -sSI http://localhost:6400/beta/
HTTP/1.1 200 OK
Date: Mon, 03 Aug 2026 11:13:52 GMT
Server: Apache/2.4.62 (CentOS Stream)
Last-Modified: Mon, 03 Aug 2026 11:10:32 GMT
ETag: "75-65822963a4a58"
Accept-Ranges: bytes
Content-Length: 117
Content-Type: text/html; charset=UTF-8

[banner@stapp03 ~]$ curl -sSI http://localhost:6400/games/
HTTP/1.1 200 OK
Date: Mon, 03 Aug 2026 11:14:07 GMT
Server: Apache/2.4.62 (CentOS Stream)
Last-Modified: Mon, 03 Aug 2026 11:10:32 GMT
ETag: "76-65822963a5610"
Accept-Ranges: bytes
Content-Length: 118
Content-Type: text/html; charset=UTF-8
```