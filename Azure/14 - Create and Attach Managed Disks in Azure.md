# 14 - Create and Attach Managed Disks in Azure

## 📋 Task Overview

<div class="flex flex-col"><!----><div class="markdown-body text-base">


<meta charset="utf-8">


<p>The Nautilus DevOps team is strategizing the migration of a portion of their infrastructure to the Azure cloud. Recognizing the scale of this undertaking, they have opted to approach the migration in incremental steps rather than as a single massive transition. To achieve this, they have segmented large tasks into smaller, more manageable units. This granular approach enables the team to execute the migration in gradual phases, ensuring smoother implementation and minimizing disruption to ongoing operations. By breaking down the migration into smaller tasks, the Nautilus DevOps team can systematically progress through each stage, allowing for better control, risk mitigation, and optimization of resources throughout the migration process.</p>
<p>Create a managed disk with the following requirements: </p>
<p></p><li>Name of the disk should be <code>xfusion-disk</code>.</li><br>
<li>Disk <code>type</code> must be <code>Standard_LRS</code>.</li><br>
<li>Disk <code>size</code> must be <code>2 GiB</code>.</li>

</div>

---

## 🚀 Complete Solution

1. **Initiate Managed Disk Creation:**
Navigated to the Azure Portal (or used Azure CLI) to provision a new standalone Managed Disk.
2. **Configure Disk Parameters:**
Applied the specific parameters required by the Nautilus architecture team:
* **Resource Group:** Selected the pre-existing lab resource group (`kml_rg_main-...`).
* **Region/Location:** `eastus` (or the default assigned region).
* **Disk Name:** `xfusion-disk`
* **Disk Size:** `2 GiB`
* **Account Type / SKU:** `Standard_LRS` (Locally Redundant Storage for cost-effective, single-datacenter redundancy).


3. **Validation & Provisioning:**
Reviewed the configuration and executed the creation. Azure returned a `Succeeded` provisioning state, verifying the disk was ready for the next phase of the migration.

### 🖥️ Proof of Execution

Below is the JSON resource output confirming the successful creation of the managed disk with the exact requested specifications:

```json
{
    "apiVersion": "2026-03-02",
    "id": "/subscriptions/.../providers/Microsoft.Compute/disks/xfusion-disk",
    "name": "xfusion-disk",
    "type": "microsoft.compute/disks",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "location": "eastus",
    "zones": [
        "1"
    ],
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 2,
        "diskIOPSReadWrite": 500,
        "diskMBpsReadWrite": 60,
        "encryption": {
            "type": "EncryptionAtRestWithPlatformKey"
        },
        "networkAccessPolicy": "AllowAll",
        "publicNetworkAccess": "Enabled",
        "provisioningState": "Succeeded",
        "diskState": "Unattached",
        "diskSizeBytes": 2147483648
    }
}

```