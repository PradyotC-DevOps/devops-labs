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
