# 17 - Create a Public Azure Blob Storage Container

## 📋 Task Overview

<div class="px-12 pb-4"><!----><div class="mt-4"><!----><div class="flex flex-col"><!----><div class="markdown-body text-base">


<meta charset="utf-8">


<p>As part of the data migration process, the Nautilus DevOps team is actively creating several storage containers on Azure. They plan to utilize public Blob containers to store the relevant data. Given the ongoing migration of other infrastructure to Azure, it is logical to consolidate data storage within the Azure environment as well.</p>
<p>Create a new storage account named <code>xfusionst23933</code> and a <code>public</code> Blob container named <code>xfusion-blob-28989</code> within the storage account. Make sure <code>anonymous read access for containers and blobs</code> is enabled.</p>

</div>

---

## 🚀 Complete Solution

1. **Provision the Storage Account:**
* Navigated to the Azure Portal and initiated the creation of a new Storage Account.
* Assigned the resource to the designated lab resource group in the `East US` region.
* Configured the following base settings:
* **Storage account name:** `xfusionst23933`
* **Performance:** `Standard`
* **Redundancy:** `Read-access geo-redundant storage (RA-GRS)`


* **Security Configuration:** Explicitly set **Blob anonymous access** to `Enabled` at the account level. This is a prerequisite; if this is disabled, individual containers cannot be made public.


2. **Create the Public Blob Container:**
* Once the storage account deployment succeeded, navigated to the **Containers** blade.
* Created a new container with the following specifications:
* **Name:** `xfusion-blob-28989`
* **Public access level:** `Container (anonymous read access for containers and blobs)`.




3. **Validation:**
* Reviewed the Activity Log to confirm the `Put blob container` operation successfully executed against the new storage account, verifying the infrastructure was correctly established.



**💡 Key Learnings & Gotchas:**

* **Two-Tier Public Access:** Azure uses a two-tier system for public access. First, the *Storage Account* must have "Allow Blob anonymous access" enabled. Second, the individual *Container* must have its public access level set to "Blob" or "Container". If the account-level setting is disabled, the container-level setting is grayed out and overridden, ensuring accidental exposure is prevented.
* **Security Posture:** While public containers are necessary for serving public assets (like website images or open datasets), they flag heavily in cloud security posture management (CSPM) tools like Microsoft Defender for Cloud. It is best practice to keep public data in dedicated, isolated storage accounts (like `xfusionst23933`) rather than mixing public and private containers in the same account.

### 🖥️ Proof of Execution

Below is a snippet of the deployment configuration verifying the creation of the `xfusionst23933` storage account with anonymous access enabled, followed by the Azure Activity Log JSON proving the successful creation of the `xfusion-blob-28989` container.

```yaml
Basics
  Subscription: Azure Free Labs
  Resource group: kml_rg_main-0a1fc9f56294421f
  Location: East US
  Storage account name: xfusionst23933

Security
  Secure transfer: Enabled
  Blob anonymous access: Enabled # Prerequisite for public containers
  Allow storage account key access: Enabled

```

**Activity Log JSON (Container Creation):**

```json
{
    "authorization": {
        "action": "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "scope": "/subscriptions/.../providers/Microsoft.Storage/storageAccounts/xfusionst23933/blobServices/default/containers/xfusion-blob-28989"
    },
    "eventName": {
        "value": "BeginRequest",
        "localizedValue": "Begin request"
    },
    "eventTimestamp": "2026-08-03T10:52:25.8224684Z",
    "operationName": {
        "value": "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "localizedValue": "Put blob container"
    },
    "resourceId": "/subscriptions/.../providers/Microsoft.Storage/storageAccounts/xfusionst23933/blobServices/default/containers/xfusion-blob-28989",
    "status": {
        "value": "Started",
        "localizedValue": "Started"
    }
}

```