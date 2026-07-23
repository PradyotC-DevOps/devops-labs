# 3 — Configure Jenkins User Access

## 📋 Task Overview

The Nautilus team is integrating Jenkins into their CI/CD pipelines. After setting up a new Jenkins server, they need to configure user access for the development team. This task focuses on user management, authentication, and authorization using Project-based Matrix Authorization Strategy.

### 🎯 Requirements

1. **Create Jenkins user** named `john` with credentials:

   - Username: `john`
   - Password: `ksH85UJjhb`
   - Full Name: `John`

2. **Enable Project-based Matrix Authorization Strategy** for fine-grained access control

3. **Grant john user permissions:**

   - Overall: Read permission
   - Job: Read permission on existing jobs

4. **Remove Anonymous user permissions** while maintaining admin privileges

5. **Configure job-level authorization** for the john user

### 📝 Access Information

- **Jenkins URL**: Accessible via Jenkins button on top bar
- **Admin Username**: `admin`
- **Admin Password**: `Adm!n321`
- **New User**: `john` / `ksH85UJjhb`

---

## 🚀 Complete Solution

### Step 1: Install Matrix Authorization Strategy Plugin

1. Navigate to **Manage Jenkins** > **Plugins** (or **Manage Plugins**).
2. Under the **Available plugins** tab, search for `Matrix Authorization Strategy`.
3. Install the plugin and restart Jenkins if required to load the new authorization modules.

---

### Step 2: Create New User Profile

1. Navigate to **Manage Jenkins** > **Users** (or **Manage Users**).
2. Click **Create User** and fill in the developer credentials:
   - **Username**: `john`
   - **Password**: `ksH85UJjhb`
   - **Confirm password**: `ksH85UJjhb`
   - **Full Name**: `John`
3. Click **Create User**.

---

### Step 3: Configure Global Security (System-Level Access Control)

1. Navigate to **Manage Jenkins** > **Security** (or **Configure Global Security**).
2. Under the **Authorization** section, select **Project-based Matrix Authorization Strategy**.
3. Add the user `john` to the authorization matrix:
   - Grant `john` the **Overall: Read** permission only.
4. Ensure the `admin` user retains full administrative permissions across all categories.
5. Locate the **Anonymous** user row and uncheck all boxes to prevent unauthenticated access.
6. Click **Save**.

---

### Step 4: Configure Job-Level Authorization (Pipeline Access)

1. Open the target pipeline/job configuration page.
2. In the job properties section, check **Enable project-based security**.
3. Add `john` to the project authorization matrix.
4. Grant `john` the **Job: Read** permission to allow viewing the pipeline and build history without edit/execution rights.
5. Click **Save**.

---

### Step 5: Verification

1. Log out of the `admin` account.
2. Log in using `john` / `ksH85UJjhb`.
3. Confirm `john` can view the specific pipeline and its build history, while system configuration options under **Manage Jenkins** remain restricted.