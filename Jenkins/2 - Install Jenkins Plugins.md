# 2 — Install Jenkins Plugins

## 📋 Task Overview

The Nautilus DevOps team has recently set up a Jenkins server for CI/CD workflows. Before creating jobs, they need to install essential plugins that will be used across most pipelines. This task focuses on installing the Git and GitLab plugins through the Jenkins web interface.

### 🎯 Requirements

1. **Access Jenkins UI** using the provided credentials
2. **Install plugins:**
   - Git plugin
   - GitLab plugin
3. **Restart Jenkins** after plugin installation (if required)
4. **Verify** plugin installation success

### 📝 Access Information

- **Jenkins URL**: Accessible via Jenkins button on top bar
- **Username**: `admin`
- **Password**: `Adm!n321`

---

## 🚀 Complete Solution

### Step 1: Access Jenkins UI

1. Open the Jenkins server interface at `http://<jenkins-server-ip>:8080`.
2. Log in using the admin credentials (`admin` / `Adm!n321`).

---

### Step 2: Navigate to Plugin Manager

1. From the main menu, navigate to **Manage Jenkins** > **Plugins** (or **Manage Plugins**).

---

### Step 3: Install Git & GitLab Plugins

1. Switch to the **Available plugins** tab.
2. **Install Git Plugin**:
   - Search for `Git`.
   - Select the official **Git** plugin (published by the Jenkins Project).
3. **Install GitLab Plugin**:
   - Clear the search filter and search for `GitLab`.
   - Select the **GitLab** plugin (published by the GitLab Community) to enable webhook triggers and GitLab API integration.

---

### Step 4: Apply Changes and Restart Jenkins

1. Click **Download now and install after restart**.
2. Check the option **Restart Jenkins when installation is complete and no jobs are running** to ensure all dependencies (`git-client`, `credentials`, etc.) are properly loaded.

---

### Step 5: Verify Plugin Installation

1. Once Jenkins restarts, log back into the UI.
2. Navigate to **Manage Jenkins** > **Plugins** > **Installed plugins**.
3. Verify both **Git** and **GitLab** plugins are active and successfully installed.