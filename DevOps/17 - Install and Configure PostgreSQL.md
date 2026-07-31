# 17 - Install and Configure PostgreSQL

## 📋 Task Overview

<div class="markdown-body text-base">


<meta charset="utf-8">


<p>The <code>Nautilus</code> application development team has shared that they are planning to deploy one newly developed application on <code>Nautilus</code> infra in <code>Stratos DC</code>. The application uses PostgreSQL database, so as a pre-requisite we need to set up PostgreSQL database server as per requirements shared below:<br><br></p>

</div><br><div class="markdown-body text-sm mb-8">


<meta charset="utf-8">


<p>PostgreSQL database server is already installed on the <code>Nautilus</code> database server.<br><br></p>
<p>a. Create a database user <code>kodekloud_top</code> and set its password to <code>Rc5C9EyvbU</code>.<br><br></p>
<p>b. Create a database <code>kodekloud_db7</code> and grant full permissions to user <code>kodekloud_top</code> on this database.<br><br></p>
<p><code>Note:</code> Please do not try to restart PostgreSQL server service.</p>
</div>

---

## 🚀 Complete Solution

1. **Connect to the Database Server:**
* SSH'd into the designated database node (`stdb01`) from the jump host.


2. **Access the Database Prompt:**
* Switched from the default login user to the `postgres` administrative system user.
* Launched the interactive PostgreSQL client terminal (`psql`).
```bash
sudo -i -u postgres
psql

```




3. **Provision User and Database:**
* Created the required database user (`kodekloud_top`) with the requested password.
* Initialized the target database (`kodekloud_db7`).
* Granted complete administrative privileges over the newly created database to the new user.
```sql
CREATE USER kodekloud_top WITH PASSWORD 'Rc5C9EyvbU';
CREATE DATABASE kodekloud_db7;
GRANT ALL PRIVILEGES ON DATABASE kodekloud_db7 TO kodekloud_top;

```




4. **Validation:**
* Exited the database using `\q` and verified completion. Strict adherence to the requirements was maintained by explicitly avoiding any `systemctl restart postgresql` commands.



**💡 Key Learnings & Gotchas:**

* **`psql` vs `postgres`:** When interacting with PostgreSQL via CLI, the `postgres` command executes the actual server daemon, while `psql` is the interactive client used to run queries.
* **Linux User Context:** By default, PostgreSQL relies on "peer authentication" for local connections. This means you must switch your Linux user context to `postgres` (`sudo -i -u postgres`) to log into the database as the master administrator without needing a password.

### 🖥️ Proof of Execution

Below is the terminal trace demonstrating the successful transition to the `postgres` user, the execution of the SQL commands to provision the database and user, and the confirmation outputs from the `psql` client.

```bash
thor@jump-host ~$ ssh peter@stdb01
peter@stdb01's password: 
[peter@stdb01 ~]$ sudo -i -u postgres
[sudo] password for peter: 

[postgres@stdb01 ~]$ psql
psql (13.23)
Type "help" for help.

postgres=# CREATE USER kodekloud_top WITH PASSWORD 'Rc5C9EyvbU';
CREATE ROLE
postgres=# CREATE DATABASE kodekloud_db7;
CREATE DATABASE
postgres=# GRANT ALL PRIVILEGES ON DATABASE kodekloud_db7 TO kodekloud_top;
GRANT
postgres=# \q

[postgres@stdb01 ~]$ exit
logout
[peter@stdb01 ~]$

```