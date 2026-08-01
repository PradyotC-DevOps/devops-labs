# 16 - Create a Private Azure Blob Storage Container

## 📋 Task Overview

<div class="flex flex-col"><!----><div class="markdown-body text-base">


<meta charset="utf-8">


<p>As part of the data migration process, the Nautilus DevOps team is actively creating several storage containers on Azure. They plan to utilize private Blob containers to store the relevant data. Given the ongoing migration of other infrastructure to Azure, it is logical to consolidate data storage within the Azure environment as well.</p>
<p>Create a new storage account named <code>xfusionst2637</code> and a <code>private</code> Blob container named <code>xfusion-blob-21691</code> within the storage account.</p>

</div>

</div>

---

## 🚀 Complete Solution

1. **Provision the Storage Account:**
* Navigated to the Azure Portal and initiated the creation of a new Storage Account.
* Assigned the resource to the designated lab resource group.
* Configured the following base settings:
* **Storage account name:** `xfusionst2637`
* **Region:** `East US`
* **Performance:** `Standard`
* **Redundancy:** `Read-access geo-redundant storage (RA-GRS)`


* **Security Hardening:** Explicitly disabled **Blob anonymous access** at the account level to ensure no containers could accidentally be exposed to the public internet.


2. **Create the Private Blob Container:**
* Once the storage account deployment succeeded, navigated to the **Containers** blade under Data Storage.
* Created a new container with the following specifications:
* **Name:** `xfusion-blob-21691`
* **Public access level:** `Private (no anonymous access)`




3. **Validation:**
* Verified that the container was successfully created within the storage account and that public access was entirely blocked, fulfilling the strict privacy requirements for the migrated data.



**💡 Key Learnings & Gotchas:**

* **Account-Level vs. Container-Level Security:** Azure allows you to set public access levels on individual containers. However, a best practice in DevOps and Cloud Security is to disable "Blob anonymous access" at the *Storage Account* level (as done in Step 1). This acts as a global safeguard—even if a developer accidentally sets a container's access level to "Public", the account-level block will override it and keep the data private.
* **Storage Account Naming Rules:** Azure Storage Account names must be globally unique across all of Azure, between 3 and 24 characters in length, and contain only lowercase letters and numbers (no hyphens or underscores). Container names, however, can contain hyphens.

### 🖥️ Proof of Execution

Below is a snippet of the deployment configuration verifying the successful creation of the `xfusionst2637` storage account with strict private access enforced.

```yaml
Basics
  Subscription: Azure Free Labs
  Resource group: kml_rg_main-07aaca7f7ab24258
  Location: East US
  Storage account name: xfusionst2637
  Primary service: Azure Blob Storage or Azure Data Lake Storage
  Performance: Standard
  Replication: Read-access geo-redundant storage (RA-GRS)

Security
  Secure transfer: Enabled
  Blob anonymous access: Disabled # Enforces private access globally
  Allow storage account key access: Enabled
  Minimum TLS version: Version 1.2

```