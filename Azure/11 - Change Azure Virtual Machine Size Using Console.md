# 11 - Change Azure Virtual Machine Size Using Console

## 📋 Task Overview

The Nautilus DevOps team is migrating a portion of its infrastructure to Azure. During the migration, they have created several virtual machines (VMs) in different regions. The team identified one VM that is experiencing increased workload demands and requires additional compute resources to maintain optimal performance.

1. Change the VM size from `Standard_B1s` to `Standard_B2s` for the virtual machine named `devops-vm`.
2. Ensure the VM is in the `running` state after the size change is complete.

**Notes:**

* Create the resources only in `southcentralus` region.
* Make sure the VM is in the `Running` state after resizing.


---

## 🚀 Complete Solution

### Step 1: Locate the Virtual Machine in Azure Portal

1. Log into the **Azure Portal**.
2. In the top search bar or left navigation pane, select **Virtual Machines**.
3. Click on the VM named **devops-vm** located in resource group `<resource-group-name>`.
4. Verify its current size is listed as `Standard_B1s`.

---

### Step 2: Deallocate / Stop Virtual Machine

1. On the **Overview** page for `devops-vm`, click **Stop** in the top action toolbar.
2. Confirm the stop prompt to deallocate compute resources safely prior to resizing.
3. Wait until the status displays **Stopped (deallocated)**.

---

### Step 3: Change Virtual Machine Size

1. In the left sidebar menu of `devops-vm`, scroll down to **Settings** > **Size** (or **Availability + scale** > **Size**).
2. Filter or search the size list for **Standard_B2s** (2 vCPUs, 4 GiB RAM).
3. Select **Standard_B2s** and click **Resize** at the bottom of the screen.
4. Wait for the notification confirming **Successfully resized virtual machine 'devops-vm'**.

---

### Step 4: Start Virtual Machine & Verify Status

1. Return to the **Overview** blade for `devops-vm`.
2. Click **Start** in the top menu bar.
3. Wait for the status to change to **Running** and verify **Provisioning state: Succeeded**.

---

## 📊 Activity Log & Verification

### 📋 Activity Log Trail

| Operation Name | Status | Timestamp | Initiated By |
|---|---|---|---|
| **Deallocate Virtual Machine** | `Succeeded` | `2026-07-23 22:58:14 GMT+0530` | `<lab-user>@azurefreekmlprod.onmicrosoft.com` |
| **Create or Update Virtual Machine** (Resize) | `Succeeded` | `2026-07-23 22:58:43 GMT+0530` | `<lab-user>@azurefreekmlprod.onmicrosoft.com` |
| **Start Virtual Machine** | `Succeeded` | `2026-07-23 22:59:16 GMT+0530` | `<lab-user>@azurefreekmlprod.onmicrosoft.com` |

---

### 📄 Azure Resource JSON Definition (Post-Resize)

```json
{
    "apiVersion": "2025-04-01",
    "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines/DEVOPS-VM",
    "name": "devops-vm",
    "type": "microsoft.compute/virtualmachines",
    "location": "southcentralus",
    "tags": {},
    "properties": {
        "hardwareProfile": {
            "vmSize": "Standard_B2s"
        },
        "provisioningState": "Succeeded",
        "vmId": "<vm-id>",
        "storageProfile": {
            "imageReference": {
                "publisher": "Canonical",
                "offer": "0001-com-ubuntu-server-jammy",
                "sku": "22_04-lts-gen2",
                "version": "latest",
                "exactVersion": "22.04.202607140"
            },
            "osDisk": {
                "osType": "Linux",
                "name": "devops-vm_disk1_<disk-id>",
                "createOption": "FromImage",
                "caching": "ReadWrite",
                "managedDisk": {
                    "storageAccountType": "Standard_LRS",
                    "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Compute/disks/devops-vm_disk1_<disk-id>"
                },
                "deleteOption": "Detach",
                "diskSizeGB": 30
            },
            "dataDisks": [],
            "diskControllerType": "SCSI"
        },
        "osProfile": {
            "computerName": "devops-vm",
            "linuxConfiguration": {
                "disablePasswordAuthentication": true,
                "provisionVMAgent": true
            },
            "adminUsername": "azureuser"
        },
        "securityProfile": {
            "securityType": "TrustedLaunch"
        }
    }
}
```