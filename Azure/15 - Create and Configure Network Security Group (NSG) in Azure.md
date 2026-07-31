# 15 - Create and Configure Network Security Group (NSG) in Azure

## 📋 Task Overview

<div class="flex flex-col"><!----><div class="markdown-body text-base">


<meta charset="utf-8">


<p>The Nautilus DevOps team is strategizing the migration of a portion of their infrastructure to the Azure cloud. Recognizing the scale of this undertaking, they have opted to approach the migration in incremental steps rather than as a single massive transition. To achieve this, they have segmented large tasks into smaller, more manageable units. This granular approach enables the team to execute the migration in gradual phases, ensuring smoother implementation and minimizing disruption to ongoing operations. By breaking down the migration into smaller tasks, the Nautilus DevOps team can systematically progress through each stage, allowing for better control, risk mitigation, and optimization of resources throughout the migration process.</p>
<p>For this task, create a network security group (NSG) with the following requirements: </p>
<p></p><li>Name of the NSG should be <code>xfusion-nsg</code>.</li><br>
<li>Add an inbound security rule named <code>Allow-HTTP</code> for <code>HTTP</code> service on port <code>80</code>, with the source CIDR range of <code>0.0.0.0/0</code>.</li><br>
<li>Add another inbound security rule named <code>Allow-SSH</code> for <code>SSH</code> service on port <code>22</code>, with the source CIDR range of <code>0.0.0.0/0</code>.</li>

</div>

---

## 🚀 Complete Solution

1. **Provision the Network Security Group:**
* Navigated to the Azure Portal and initiated the creation of a new Network Security Group.
* Assigned the resource to the designated lab resource group (`kml_rg_main-...`) in the `eastus` region.
* Named the NSG `xfusion-nsg`.


2. **Configure Inbound Security Rules:**
* **HTTP Access:** Added an inbound rule named `Allow-HTTP` to permit web traffic. Set the destination port to `80`, the protocol to `TCP`, and the source CIDR to `0.0.0.0/0` (represented as `*` in Azure) to allow public internet access. Priority was set to `100`.
* **SSH Access:** Added a second inbound rule named `Allow-SSH` to permit secure administrative access. Set the destination port to `22`, the protocol to `TCP`, and the source CIDR to `0.0.0.0/0` (`*`). Priority was set to `110`.


3. **Validation:**
* Reviewed the Activity Log to confirm the `Create or Update Network Security Group` event reached a `Succeeded` state.
* Exported the resource JSON to verify that the custom rules were successfully injected alongside Azure's default VNet and Load Balancer rules.



**💡 Key Learnings & Gotchas:**

* **Azure CIDR Notation:** In AWS, you explicitly type `0.0.0.0/0` to allow all traffic. In Azure's backend JSON, this is often translated and represented simply as `*` (Any).
* **Rule Priorities:** NSG rules are processed in priority order, with lower numbers processed first. It is a best practice to leave gaps between your rule numbers (e.g., 100, 110, 120) so you can easily insert new rules later without having to renumber everything.
* **Default Rules:** Notice in the JSON that Azure automatically attaches default rules with priorities like `65000` and `65500` (e.g., `DenyAllInBound`). Because our custom rules have lower priority numbers (100 and 110), they override the default deny block.

### 🖥️ Proof of Execution

Below is the JSON resource output confirming the successful creation of the `xfusion-nsg` Network Security Group and the exact configuration of the `Allow-HTTP` and `Allow-SSH` security rules.

```json
{
    "apiVersion": "2025-07-01",
    "id": "/subscriptions/.../providers/Microsoft.Network/networkSecurityGroups/xfusion-nsg",
    "name": "xfusion-nsg",
    "type": "microsoft.network/networksecuritygroups",
    "location": "eastus",
    "properties": {
        "provisioningState": "Succeeded",
        "securityRules": [
            {
                "name": "Allow-HTTP",
                "type": "Microsoft.Network/networkSecurityGroups/securityRules",
                "properties": {
                    "provisioningState": "Succeeded",
                    "protocol": "TCP",
                    "sourcePortRange": "*",
                    "destinationPortRange": "80",
                    "sourceAddressPrefix": "*",
                    "destinationAddressPrefix": "*",
                    "access": "Allow",
                    "priority": 100,
                    "direction": "Inbound"
                }
            },
            {
                "name": "Allow-SSH",
                "type": "Microsoft.Network/networkSecurityGroups/securityRules",
                "properties": {
                    "provisioningState": "Succeeded",
                    "protocol": "TCP",
                    "sourcePortRange": "*",
                    "destinationPortRange": "22",
                    "sourceAddressPrefix": "*",
                    "destinationAddressPrefix": "*",
                    "access": "Allow",
                    "priority": 110,
                    "direction": "Inbound"
                }
            }
        ],
        "defaultSecurityRules": [
            {
                "name": "AllowVnetInBound",
                "properties": {
                    "description": "Allow inbound traffic from all VMs in VNET",
                    "protocol": "*",
                    "sourceAddressPrefix": "VirtualNetwork",
                    "destinationAddressPrefix": "VirtualNetwork",
                    "access": "Allow",
                    "priority": 65000,
                    "direction": "Inbound"
                }
            },
            {
                "name": "DenyAllInBound",
                "properties": {
                    "description": "Deny all inbound traffic",
                    "protocol": "*",
                    "sourceAddressPrefix": "*",
                    "destinationAddressPrefix": "*",
                    "access": "Deny",
                    "priority": 65500,
                    "direction": "Inbound"
                }
            }
        ]
    }
}

```