# 20 - Configure Nginx + PHP-FPM Using Unix Sock

## 📋 Task Overview

<div class="flex flex-col"><!----><div class="markdown-body text-base">


<meta charset="utf-8">


<p>The <code>Nautilus</code> application development team is planning to launch a new PHP-based application, which they want to deploy on <code>Nautilus</code> infra in <code>Stratos DC</code>. The development team had a meeting with the production support team and they have shared some requirements regarding the infrastructure. Below are the requirements they shared:</p>

</div><br><div class="markdown-body text-sm mb-8">


<meta charset="utf-8">


<p>a. Install <code>nginx</code> on <code>app server 3</code> , configure it to use port <code>8093</code> and its document root should be <code>/var/www/html</code>. </p>
<p>b. Install <code>php-fpm</code> version <code>8.1</code> on <code>app server 3</code>, it must use the unix socket <code>/var/run/php-fpm/default.sock</code> (create the parent directories if don't exist). </p>
<p>c. Configure php-fpm and nginx to work together. </p>
<p>d. Once configured correctly, you can test the website using <code>curl http://stapp03:8093/index.php</code> command from jump host.</p>
<p>NOTE: We have copied two files, <code>index.php</code> and <code>info.php</code>, under <code>/var/www/html</code> as part of the <code>PHP-based application</code> setup. Please do not modify these files.</p>

</div>

---

## 🚀 Complete Solution

**1. Install Nginx and Third-Party Repositories**

* **Action:** Installed Nginx, the EPEL release, and the Remi repository.
```bash
sudo yum install nginx epel-release -y
sudo dnf install https://rpms.remirepo.net/enterprise/remi-release-$(rpm -E %rhel).rpm -y

```


* **The "Why":** Enterprise Linux distributions (like CentOS Stream) prioritize stability over bleeding-edge software. The default repositories did not contain PHP 8.1. The **Remi repository** is the industry standard for securing newer, supported builds of PHP on RHEL-based systems.

**2. Install PHP-FPM 8.1**

* **Action:** Installed the specific PHP 8.1 FPM package from the Remi-safe repository.
```bash
sudo yum install php81-php-fpm.x86_64 -y

```


* **The "Why":** `php-fpm` (FastCGI Process Manager) is an alternative PHP FastCGI implementation with highly advanced features for heavy-loaded sites. Unlike the old `mod_php` which ran inside Apache, FPM runs as a standalone service, allowing Nginx to remain lightweight and only pass PHP requests when necessary.

**3. Configure PHP-FPM & Unix Socket Permissions**

* **Action:** Created the socket directory and modified `/etc/opt/remi/php81/php-fpm.d/www.conf`.
```bash
sudo mkdir -p /var/run/php-fpm/
# Inside www.conf:
listen = /var/run/php-fpm/default.sock
user = nginx
group = nginx
listen.owner = nginx
listen.group = nginx
listen.acl_users = nginx # CRITICAL FIX

```


* **The "Why":**
* **Unix Sockets vs TCP:** We configured `listen` to use a `.sock` file instead of `127.0.0.1:9000`. Unix sockets use Inter-Process Communication (IPC) directly on the kernel level, bypassing the network stack completely. This is significantly faster and more secure for services running on the *same* machine.
* **The ACL Gotcha:** By default, the Remi PHP package sets `listen.acl_users = apache`. ACLs (Access Control Lists) take precedence over standard Unix file ownership. If this was not changed to `nginx`, Nginx would receive a "Permission Denied" when trying to hand off traffic to the socket, effectively breaking the application.



**4. Configure Nginx Server Block**

* **Action:** Modified the main `/etc/nginx/nginx.conf` file.
```nginx
server {
    listen       8093;
    listen       [::]:8093;
    server_name  _;
    root         /var/www/html;

    location ~ \.php$ {
        root           /var/www/html;
        fastcgi_pass   unix:/var/run/php-fpm/default.sock;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include        fastcgi_params;
    }
    # ... (error pages omitted)
}

```


* **The "Why":**
* `listen 8093;`: Changes the default HTTP port to meet the application team's custom port requirement.
* `location ~ \.php$`: A regular expression that tells Nginx: "If a requested file ends in `.php`, do not serve it as plain text. Instead, forward it to the `fastcgi_pass` destination (our Unix socket) so PHP-FPM can execute the code and return the generated HTML."



**5. Start Services and Validate**

* **Action:** Enabled and restarted both services, then tested the connection from the jump host.
```bash
sudo systemctl enable --now php81-php-fpm nginx

```



**💡 Key Learnings & Gotchas (The Phantom 404 Error):**
During initial testing, Nginx returned a `404 Not Found` instead of a `502 Bad Gateway` when the socket permissions were incorrect. Why? Because when Nginx got "Permission Denied" from the socket, it generated an internal `502` error. It then looked at its config, which said `error_page 500 502 /50x.html;`. Nginx tried to find `/50x.html` inside the newly defined `/var/www/html` root directory. Because the file wasn't there, it returned a `404` for the missing error page! This is a classic Nginx debugging trap.

### 🖥️ Proof of Execution

Below is the terminal trace demonstrating the final service restarts and the successful validation `curl` command executed from the jump host, proving Nginx and PHP-FPM are communicating flawlessly over the custom port and Unix socket.

```bash
# Restarting services on stapp03 to apply final configurations
[banner@stapp03 ~]$ sudo systemctl restart nginx
[banner@stapp03 ~]$ sudo systemctl restart php81-php-fpm

# Verifying the Unix socket was generated with correct permissions
[banner@stapp03 ~]$ sudo ls -lah /var/run/php-fpm/default.sock
srw-rw----+ 1 root root 0 Aug  6 18:38 /var/run/php-fpm/default.sock

[banner@stapp03 ~]$ exit
logout
Connection to stapp03 closed.

# Final validation from the jump-host confirming end-to-end routing
thor@jump-host ~$ curl http://stapp03:8093/index.php
Welcome to xFusionCorp Industries!

```