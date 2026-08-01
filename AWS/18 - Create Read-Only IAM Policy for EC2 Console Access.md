# 18 - Create Read-Only IAM Policy for EC2 Console Access

## 📋 Task Overview

<div class="markdown-body text-base">


<meta charset="utf-8">


<p>When establishing infrastructure on the AWS cloud, Identity and Access Management (IAM) is among the first and most critical services to configure. IAM facilitates the creation and management of user accounts, groups, roles, policies, and other access controls. The Nautilus DevOps team is currently in the process of configuring these resources and has outlined the following requirements.</p>
<p>Create an IAM policy named <code>iampolicy_john</code> in <code>us-east-1</code> region, it must allow read-only access to the EC2 console, i.e this policy must allow users to view all instances, AMIs, and snapshots in the Amazon EC2 console.</p>

</div><div class="markdown-body text-sm mb-8">

<p><code>Notes:</code> </p>
<ul>
<li><p>Create the resources only in <code>us-east-1</code> region.</p></li>
</ul>

</div>

---

## 🚀 Complete Solution

1. **Access IAM Policy Dashboard:**
* Logged into the AWS Management Console and navigated to the **IAM** service.
* Selected **Policies** from the left-hand navigation pane and clicked **Create policy**.


2. **Define Policy Permissions:**
* Utilized the Visual Editor / JSON editor to configure permissions specific to the **EC2** service.
* Filtered and selected the necessary **Read** actions (such as `ec2:Get*` and `ec2:Export*` operations) required to view EC2 resources, AMIs, and Snapshots.
* Set the **Resource** scope to `*` (All resources), as read-only console visibility typically requires querying all resources of that type within the account.


3. **Review and Provision:**
* Proceeded to the review stage and assigned the requested policy name: `iampolicy_john`.
* Clicked **Create policy** and validated that the custom policy successfully appeared in the IAM Policies directory.



**💡 Key Learnings & Gotchas:**

* **Granular Action Expansion:** When using the AWS Console's Visual Editor to select "Read" actions for a service like EC2, AWS automatically expands your selection into dozens of specific API calls (e.g., `ec2:GetConsoleOutput`, `ec2:GetSnapshotBlockPublicAccessState`). This granular approach is far more secure than writing `"Action": "ec2:*"` or relying on overly broad managed policies.
* **Global vs. Regional Scope:** Similar to IAM Users and Groups, IAM Policies are global entities. Even though the task specified creating the resource in `us-east-1` (which is where the API call is registered and logged by CloudTrail), the resulting `iampolicy_john` policy can be attached to users and evaluated globally across all AWS regions.

### 🖥️ Proof of Execution

Below is the AWS CloudTrail event log confirming the successful `CreatePolicy` API call. It captures the exact JSON policy document submitted, the region endpoint, and the resulting Amazon Resource Name (ARN) generated for the new policy.

```json
{
    "eventVersion": "1.11",
    "userIdentity": {
        "type": "IAMUser",
        "accountId": "654654579437",
        "userName": "kk_labs_user_924724"
    },
    "eventTime": "2026-08-01T11:53:30Z",
    "eventSource": "iam.amazonaws.com",
    "eventName": "CreatePolicy",
    "awsRegion": "us-east-1",
    "requestParameters": {
        "policyName": "iampolicy_john",
        "policyDocument": "{\n\t\"Version\": \"2012-10-17\",\n\t\"Statement\": [\n\t\t{\n\t\t\t\"Sid\": \"VisualEditor0\",\n\t\t\t\"Effect\": \"Allow\",\n\t\t\t\"Action\": [\n\t\t\t\t\"ec2:GetIpamResourceCidrs\",\n\t\t\t\t\"ec2:GetInstanceUefiData\",\n\t\t\t\t\"ec2:GetSnapshotBlockPublicAccessState\",\n\t\t\t\t\"ec2:GetConsoleScreenshot\",\n\t\t\t\t\"ec2:GetConsoleOutput\",\n\t\t\t\t\"ec2:GetSecurityGroupsForVpc\"\n\t\t\t\t/* [...] Additional Read/Get Actions Omitted for Brevity */\n\t\t\t],\n\t\t\t\"Resource\": \"*\"\n\t\t}\n\t]\n}"
    },
    "responseElements": {
        "policy": {
            "policyName": "iampolicy_john",
            "policyId": "ANPAZQ3DUELWUMAOFJQKY",
            "arn": "arn:aws:iam::654654579437:policy/iampolicy_john",
            "path": "/",
            "defaultVersionId": "v1",
            "isAttachable": true,
            "createDate": "2026-08-01T11:53:30Z"
        }
    },
    "eventType": "AwsApiCall",
    "managementEvent": true,
    "eventCategory": "Management"
}

```