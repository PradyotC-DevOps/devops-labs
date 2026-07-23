# 13 - Create AMI from EC2 Instance

## 📋 Task Overview

The Nautilus DevOps team is strategizing the migration of a portion of their infrastructure to the AWS cloud. Recognizing the scale of this undertaking, they have opted to approach the migration in incremental steps rather than as a single massive transition. To achieve this, they have segmented large tasks into smaller, more manageable units. This granular approach enables the team to execute the migration in gradual phases, ensuring smoother implementation and minimizing disruption to ongoing operations. By breaking down the migration into smaller tasks, the Nautilus DevOps team can systematically progress through each stage, allowing for better control, risk mitigation, and optimization of resources throughout the migration process.

For this task, create an AMI from an existing EC2 instance named `devops-ec2` with the following requirement:

Name of the AMI should be `devops-ec2-ami`, make sure AMI is in `available` state.

`Notes:`

   - Create the resources only in `us-east-1` region.
---

## 🚀 Complete Solution

Here is the step-by-step procedure to create an Amazon Machine Image (AMI) from an existing EC2 instance directly via the AWS Management Console.

1. **Navigate to the EC2 Dashboard:** Verify the region constraint.
Log in to the AWS Management Console.

1. In the top-right corner of the navigation bar, ensure your selected region is set to **N. Virginia (`us-east-1`)**.
2. Search for **EC2** in the top search bar and select it to open the EC2 Dashboard.


2. **Select the Target Instance:**
1. On the left-hand navigation pane, under **Instances**, click on **Instances**.
2. Locate the instance named **`devops-ec2`** in the list.
3. Select it by checking the box next to its name.


3. **Initiate Image Creation:** The instance may briefly reboot unless 'No reboot' is selected.
With the `devops-ec2` instance selected:

1. Click the **Actions** dropdown menu at the top right.
2. Hover over **Image and templates**.
3. Select **Create image**.


4. **Configure AMI Details:**
You will be taken to the "Create image" configuration page. Fill out the required details:

* **Image name:** Enter exactly **`devops-ec2-ami`**.
* **Image description:** (Optional) You can add a brief description like "AMI for devops-ec2 backup".
* Leave all other volume and instance settings as their default values.

Click the **Create image** button at the bottom of the page. You will see a green success banner containing the new AMI ID.


5. **Verify the AMI Status:** Fulfills the 'available' state requirement.
Creating an AMI takes a few minutes as AWS snapshots the underlying EBS volumes.

1. On the left-hand navigation pane, scroll down to the **Images** section and click on **AMIs**.
2. Change the filter from "Owned by me" to "Public images" if needed, but usually, your newly created AMI will appear under **Owned by me**.
3. Locate **`devops-ec2-ami`** in the list.
4. Check the **Status** column. Initially, it will say `pending`.
5. Click the refresh icon periodically until the status changes to **`available`**.

Once the status is `available`, the task is successfully complete!


---

## 📊 AWS AMI Creation Final Configuration State

The AMI creation operation was executed perfectly. Below are the key verified attributes of the newly created image:

| Property | Value | Status |
| --- | --- | --- |
| **AMI Name** | `devops-ec2-ami` | ✅ Verified |
| **AMI ID** | `ami-0b5ac45b8363149a8` | ✅ Verified |
| **Status** | `Available` | ✅ Verified |
| **Region** | `us-east-1` | ✅ Verified |
| **Source Instance Base** | `devops-ec2` | ✅ Verified |
| **Architecture** | `x86_64` | ✅ Verified |

---

## 📝 Configuration Proof (Excerpt)

The following details from the Image Summary and Instance Summary confirm all requirements were satisfied:

**Image Details:**

```text
AMI ID: ami-0b5ac45b8363149a8
AMI name: devops-ec2-ami
Status: Available
Source AMI Region: us-east-1
Creation date: 2026-07-23T20:42:58.000Z

```

**Ancestry & Source Lineage:**

```text
Ancestry level: 0 (input AMI)
AMI ID: ami-0b5ac45b8363149a8
Source AMI Region: us-east-1

```

### ✅ Conclusion

The custom AMI (`devops-ec2-ami`) was successfully generated from the target EC2 instance in the `us-east-1` region. The snapshot process has fully completed, and the image is now in the **Available** state, ready for the team's ongoing migration phases.