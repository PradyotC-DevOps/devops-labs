# 4 — Organize Jenkins Jobs with Folders

## 📋 Task Overview

xFusionCorp Industries' DevOps team aims to streamline the management of Jenkins jobs by organizing them into distinct folders based on their purpose. Complete the task following the provided requirements:

1. Access the Jenkins UI by clicking on the Jenkins button in the top bar. Log in using the credentials: username `admin` and password `Adm!n321`.
2. Create a new folder named `Apache` within the Jenkins UI.
3. Move the existing jobs `httpd-php` and `services` under the newly created `Apache` folder.

> NOTE
> - Ensure to install any required plugins and restart the Jenkins service if necessary. Opt for **Restart Jenkins when installation is complete and no jobs are running** on the plugin installation/update page.
> - Be aware that the Jenkins UI may experience temporary unresponsiveness during the service restart. Refresh the UI page if needed.
> - Capture screenshots of your work for documentation and review purposes (or utilize screen recording software like `loom.com`).

---

## 🚀 Complete Solution

### Step 1: Install Folders Plugin (If required)

1. Navigate to **Manage Jenkins** > **Plugins** (or **Manage Plugins**).
2. Switch to the **Available plugins** tab and search for `Folders`.
3. Select the **Folders Plugin** and click **Download now and install after restart**.
4. Check **Restart Jenkins when installation is complete and no jobs are running** to apply the plugin.

---

### Step 2: Create a New Folder

1. From the main Jenkins dashboard, click **New Item** in the left navigation menu.
2. Enter `Apache` as the item name.
3. Select **Folder** as the item type.
4. Click **OK** and then click **Save** to create the folder.

---

### Step 3: Move Existing Jobs to the Folder

1. **Move `httpd-php` Job**:
   - Go back to the Jenkins dashboard and click on the **httpd-php** job.
   - Click **Move** from the left menu.
   - Select `/Apache` as the **Destination** from the dropdown list.
   - Click **Move** to confirm.

2. **Move `services` Job**:
   - Return to the main dashboard and click on the **services** job.
   - Click **Move** from the left menu.
   - Select `/Apache` as the **Destination** from the dropdown list.
   - Click **Move** to confirm.

---

### Step 4: Verification

1. Return to the main Jenkins dashboard.
2. Click on the **Apache** folder.
3. Confirm that both `httpd-php` and `services` jobs are located inside the `Apache` folder and accessible.