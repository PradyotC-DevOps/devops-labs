# 18 - Install and Configure DB Server

## 📋 Task Overview

<div class="markdown-body text-base">


<meta charset="utf-8">


<p>We need to setup a database server on <code>Nautilus DB Server</code> in <code>Stratos Datacenter</code>. Please perform the below given steps on DB Server:</p>

</div><div class="markdown-body text-sm mb-8">


<meta charset="utf-8">


<p>a. Install/Configure MariaDB server.</p>
<p>b. Create a database named <code>kodekloud_db2</code>.</p>
<p>c. Create a user called <code>kodekloud_pop</code> and set its password to <code>YchZHRcLkL</code>.</p>
<p>d. Grant full permissions to user <code>kodekloud_pop</code> on database <code>kodekloud_db2</code>.</p>

</div></div>

---

## 🚀 Complete Solution

1. **Connect to the Database Server:**
* SSH'd into the designated database node (`stdb01`) from the jump host using the `peter` user credentials.


2. **Install and Enable MariaDB:**
* Installed the `mariadb-server` package using the `yum` package manager.
* Enabled the MariaDB service to persist across system reboots and started the daemon.
```bash
sudo yum install -y mariadb-server
sudo systemctl enable mariadb
sudo systemctl start mariadb

```




3. **Access the Database Prompt:**
* Accessed the MariaDB interactive client as the `root` database user.
```bash
sudo mysql -u root

```




4. **Provision User and Database:**
* Created the required database (`kodekloud_db2`).
* Created the required user (`kodekloud_pop`) with the requested password. *(Note: Users were created for both `localhost` and `%` (Any Host) to ensure the application servers could connect to the DB remotely).*
* Granted complete privileges over the newly created database to the new user and flushed privileges to apply changes immediately.
```sql
CREATE DATABASE kodekloud_db2;
CREATE USER 'kodekloud_pop'@'localhost' IDENTIFIED BY 'YchZHRcLkL';
CREATE USER 'kodekloud_pop'@'%' IDENTIFIED BY 'YchZHRcLkL';
GRANT ALL PRIVILEGES ON kodekloud_db2.* TO 'kodekloud_pop'@'localhost';
GRANT ALL PRIVILEGES ON kodekloud_db2.* TO 'kodekloud_pop'@'%';
FLUSH PRIVILEGES;

```





**💡 Key Learnings & Gotchas:**

* **`localhost` vs `%` (Remote Access):** In MariaDB/MySQL, user permissions are strictly bound to the host they are connecting from. If you only create `'kodekloud_pop'@'localhost'`, the application servers (`stapp01`, `stapp02`, etc.) will be rejected when they try to connect over the network. Creating the user with `'@'%'` (Any Host) is crucial for a standard multi-tier web architecture.
* **`mariadb` vs `mariadb-server`:** Running `yum install mariadb` only installs the client tools. To actually run the database engine, you must explicitly install the `mariadb-server` package.

### 🖥️ Proof of Execution

Below is the terminal trace demonstrating the installation of the MariaDB service, the startup of the daemon, and the SQL execution to provision the database and user securely.

```bash
thor@jump-host ~$ ssh peter@stdb01
peter@stdb01's password: 
[peter@stdb01 ~]$ sudo yum install -y mariadb-server
[sudo] password for peter: 
Loaded plugins: fastestmirror, ovl
Resolving Dependencies
--> Running transaction check
---> Package mariadb-server.x86_64 1:5.5.68-1.el7 will be installed
# [...] (Dependencies resolving and installation output omitted for brevity)
Installed:
  mariadb-server.x86_64 1:5.5.68-1.el7                                                                                                                  
Complete!

[peter@stdb01 ~]$ sudo systemctl enable mariadb
Created symlink from /etc/systemd/system/multi-user.target.wants/mariadb.service to /usr/lib/systemd/system/mariadb.service.
[peter@stdb01 ~]$ sudo systemctl start mariadb

[peter@stdb01 ~]$ sudo mysql -u root
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 2
Server version: 5.5.68-MariaDB MariaDB Server

MariaDB [(none)]> CREATE DATABASE kodekloud_db2;
Query OK, 1 row affected (0.00 sec)

MariaDB [(none)]> CREATE USER 'kodekloud_pop'@'localhost' IDENTIFIED BY 'YchZHRcLkL';
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> CREATE USER 'kodekloud_pop'@'%' IDENTIFIED BY 'YchZHRcLkL';
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> GRANT ALL PRIVILEGES ON kodekloud_db2.* TO 'kodekloud_pop'@'localhost';
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> GRANT ALL PRIVILEGES ON kodekloud_db2.* TO 'kodekloud_pop'@'%';
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> exit
Bye
[peter@stdb01 ~]$ 

```