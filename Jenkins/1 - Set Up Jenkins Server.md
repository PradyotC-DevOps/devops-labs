# 1 — Set Up Jenkins Server

## 📋 Task Overview

The DevOps team at xFusionCorp Industries is initiating the setup of CI/CD pipelines and has decided to utilize Jenkins as their automation server. This task involves installing Jenkins on a dedicated server, configuring it, and creating an admin user.

### 🎯 Requirements

1. **Install Jenkins** on the `jenkins` server using the `yum` utility only
2. **Start Jenkins service** and ensure it's enabled on boot
3. **Create admin user** with the following credentials:
   - **Username**: `theadmin`
   - **Password**: `Adm!n321`
   - **Full Name**: `User1`
   - **Email**: `user1@jenkins.stratos.xfusioncorp.com`

### 📝 Access Information

- **Jump Host**: SSH using root user (password: `S3curePass`)
- **Jenkins Server**: Accessible from jump host as `jenkins`
- **Jenkins UI**: Access via Jenkins button on top bar after installation

---


## 🚀 Complete Solution

### Step 1: Connect to the Jenkins Server

#### 1.1 SSH to Jump Host

```bash
# Connect to jump host
ssh root@<jump_host_ip>
# When prompted, enter password: S3curePass
```

#### 1.2 Connect to Jenkins Server

```bash
# From jump host, SSH to Jenkins server
ssh root@jenkins
```

**Expected Output:**

```
[root@jenkins ~]#
```

---

### Step 2: Prepare System for Jenkins Installation

#### 2.1 Update System Packages

```bash
# Update all packages to latest version
yum update -y
```

#### 2.2 Install Required Dependencies

```bash
# Install wget if not available
yum install wget -y

# Install curl for troubleshooting
yum install curl -y
```

---

### Step 3: Install Java (Jenkins Prerequisite)

Jenkins requires Java to run. We'll install OpenJDK 11.

#### 3.1 Install Java 11

```bash
# Install Java 11 OpenJDK
yum install java-21-openjdk-headless fontconfig -y
```

#### 3.2 Verify Java Installation

```bash
# Check Java version
java -version
```

---

### Step 4: Add Jenkins Repository

#### 4.1 Download Jenkins Repository Configuration

```bash
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/rpm-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/rpm-stable/jenkins.io-2026.key
```

#### 4.2 Import Jenkins GPG Key

```bash
sudo rpm --import https://pkg.jenkins.io/rpm-stable/jenkins.io-2026.key
```
---

### Step 5: Install Jenkins

#### 5.1 Install Jenkins Package

```bash
# Install Jenkins using yum
yum install jenkins -y
```

---

### Step 6: Configure Jenkins Service

#### 6.1 Check Jenkins Service Status

```bash
# Check if Jenkins service is installed
systemctl status jenkins
```

#### 6.2 Enable Jenkins Service

```bash
# Enable Jenkins to start on boot
systemctl enable jenkins
```

**Expected Output:**

```
Created symlink from /etc/systemd/system/multi-user.target.wants/jenkins.service to /usr/lib/systemd/system/jenkins.service.
```

#### 6.3 Start Jenkins Service

```bash
# Start Jenkins service
systemctl start jenkins
```

#### 6.4 Verify Jenkins is Running

```bash
# Check service status
systemctl status jenkins
```

#### 6.5 Check Jenkins Port

```bash
# Verify Jenkins is listening on port 8080
netstat -tulpn | grep 8080
# OR
ss -tulpn | grep 8080
```

---

### Step 7: Retrieve Initial Admin Password

#### 7.1 Get Initial Admin Password

```bash
# Display the initial admin password
cat /var/lib/jenkins/secrets/initialAdminPassword
```

**Expected Output:**

```
a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6
```

**Important:** Copy this password - you'll need it for the initial Jenkins setup.

#### 7.2 Alternative: Check Jenkins Logs

```bash
# View Jenkins startup logs
tail -50 /var/log/jenkins/jenkins.log
```

Look for lines containing the initial admin password.

---

### Step 8: Access Jenkins Web UI

#### 8.1 Open Jenkins in Browser

1. **Click the "Jenkins" button** on the top bar of your lab environment

   - This will open Jenkins UI in a new tab
   - URL will be similar to: `http://jenkins:8080`

2. **Alternative: Direct Access**

   ```bash
   # If you need to access via port forwarding or direct URL
   # Get server IP
   hostname -I

   # Access via: http://<server_ip>:8080
   ```

#### 8.2 Unlock Jenkins Screen

You'll see the "Unlock Jenkins" screen.

1. **Enter the initial admin password** (retrieved in Step 7.1)
2. **Click "Continue"**

---

### Step 9: Initial Jenkins Setup

#### 9.1 Customize Jenkins - Plugin Installation

You'll be presented with two options:

1. **Install suggested plugins** (Recommended for beginners)
2. **Select plugins to install** (Advanced users)

**Choose:** "Install suggested plugins"

**Plugins Being Installed:**

- Git plugin
- Pipeline plugin
- Credentials plugin
- SSH Slaves plugin
- Matrix Authorization Strategy
- And many more...

**Wait** for all plugins to install (usually 2-5 minutes).

#### 9.2 Handling Plugin Installation Issues

If any plugins fail to install:

```bash
# Check Jenkins logs
tail -f /var/log/jenkins/jenkins.log

# Restart Jenkins if needed
systemctl restart jenkins
```

Then refresh the browser and retry.

---

### Step 10: Create Admin User

#### 10.1 Create First Admin User

After plugin installation, you'll see "Create First Admin User" screen.

**Fill in the following details:**

| Field            | Value                                  |
| ---------------- | -------------------------------------- |
| Username         | `theadmin`                             |
| Password         | `Adm!n321`                             |
| Confirm Password | `Adm!n321`                             |
| Full Name        | `User1`                                 |
| Email Address    | `user1@jenkins.stratos.xfusioncorp.com` |

**Click:** "Save and Continue"

#### 10.2 Instance Configuration

You'll see "Instance Configuration" screen with Jenkins URL.

**Default URL:** `http://jenkins:8080/`

**Click:** "Save and Finish"

#### 10.3 Jenkins is Ready!

You'll see "Jenkins is ready!" screen.

**Click:** "Start using Jenkins"

---

### Step 11: Verify Jenkins Setup

#### 11.1 Login with Admin Credentials

1. **Enter credentials:**

   - **Username:** `theadmin`
   - **Password:** `Adm!n321`

2. **Click:** "Sign in"

#### 11.2 Verify Dashboard Access

You should now see the Jenkins Dashboard with:

- ✅ Welcome message
- ✅ Create new jobs option
- ✅ Manage Jenkins link
- ✅ Admin user logged in (top right corner)

#### 11.3 Check User Configuration

1. **Click on "theadmin"** (top right corner)
2. **Click "Configure"**
3. **Verify:**
   - Full Name: `User1`
   - Email: `user1@jenkins.stratos.xfusioncorp.com`