# 12 - Add and Manage Tags for Azure Virtual Machines

## 📋 Task Overview

The Nautilus DevOps team is migrating a portion of their infrastructure to Azure. During the migration, they have created several virtual machines (VMs) in different regions. The team has identified one VM that is not tagged properly so they decided to tag it as needed.

Add the tag `Environment=dev` to the virtual machine named `datacenter-vm`.

---

## 🚀 Complete Solution

### Execution Steps

Here is the standard procedure to apply the tag using the Azure Portal:

1. **Log in to the Azure Portal:**
Navigate to [https://portal.azure.com](https://portal.azure.com) and log in using the provided temporary credentials (`kk_lab_user_main...`).


2. **Locate the Target Virtual Machine:**
1. In the top search bar, type **Virtual machines** and select the service.
2. From the list of VMs, click on **`datacenter-vm`**.


3. **Apply the Required Tag:**
1. On the left-hand menu of the VM's overview page, click on **Tags**.
2. In the **Name** field, enter `Environment`.
3. In the **Value** field, enter `dev`.
4. Click **Apply** (or **Save**) to commit the changes.


---

## 📊 Verification and Operation Logs

Below is the sanitized configuration and activity log confirming the tag was successfully written to the resource.

### 1. VM Configuration State (Excerpt)

This snippet proves the `datacenter-vm` now successfully carries the `Environment: dev` tag.

```json
{
    "name": "datacenter-vm",
    "type": "microsoft.compute/virtualmachines",
    "location": "centralus",
    "tags": {
        "Environment": "dev"
    },
    "properties": {
        "hardwareProfile": {
            "vmSize": "Standard_B1s"
        },
        "provisioningState": "Succeeded",
        "osProfile": {
            "computerName": "datacenter-vm",
            "linuxConfiguration": {
                "disablePasswordAuthentication": true,
                "ssh": {
                    "publicKeys": [
                        {
                            "path": "/home/azureuser/.ssh/authorized_keys",
                            "keyData": "[REDACTED_SSH_PUB_KEY]"
                        }
                    ]
                }
            }
        }
    }
}

```

### 2. Azure Activity Log: Write Tags Operation

This log validates the background API call (`Microsoft.Resources/tags/write`) executed by the portal and confirms its success.

```json
{
    "authorization": {
        "action": "Microsoft.Resources/tags/write",
        "scope": "/subscriptions/.../providers/Microsoft.Compute/virtualMachines/datacenter-vm/providers/Microsoft.Resources/tags/default"
    },
    "caller": "kk_lab_user_main-c61460b34fe94e32@azurefreekmlprod.onmicrosoft.com",
    "claims": {
        "note": "[REDACTED] - JWT claims, PUID, session tokens, and IP addresses omitted for security"
    },
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "operationName": {
        "value": "Microsoft.Resources/tags/write",
        "localizedValue": "Write tags"
    },
    "resourceId": "/subscriptions/.../virtualMachines/datacenter-vm",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "OK",
        "localizedValue": "OK (HTTP Status Code: 200)"
    },
    "eventTimestamp": "2026-07-24T06:24:16.602Z"
}

```