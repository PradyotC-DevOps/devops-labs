# 17 - Create IAM Group

## 📋 Task Overview

<div class="markdown-body text-base">


<meta charset="utf-8">


<p>The Nautilus DevOps team has been creating a couple of services on AWS cloud. They have been breaking down the migration into smaller tasks, allowing for better control, risk mitigation, and optimization of resources throughout the migration process. Recently they came up with requirements mentioned below.</p>
<p>Create an IAM group named <code>iamgroup_yousuf</code>.</p>

</div><br><div class="markdown-body text-sm mb-8">


<meta charset="utf-8">

<p><code>Notes:</code> </p>
<ul>
<li><p>Create the resources only in <code>us-east-1</code> region.</p></li>
</ul>
</div>

---

## 🚀 Complete Solution

1. **Access AWS IAM Dashboard:**
* Logged into the AWS Management Console using the provided administrative credentials.
* Navigated to the **IAM (Identity and Access Management)** service.


2. **Provision New User Group:**
* Selected **User groups** from the left-hand navigation pane and clicked **Create group**.
* Entered the required group name: `iamgroup_yousuf`.
* Proceeded without adding users or attaching permissions policies, as this task focused strictly on the initial provisioning of the group resource.


3. **Review and Create:**
* Clicked **Create group** and verified the new entity successfully appeared in the IAM directory.



**💡 Key Learnings & Gotchas:**

* **IAM is a Global Service:** The task constraints noted to "Create the resources only in `us-east-1` region." However, IAM is a global service. As seen in the CloudTrail log below, IAM API calls are routed through and logged in the `us-east-1` endpoint, but the actual IAM Group spans all regions globally across the AWS account.
* **Role-Based Access Control (RBAC):** Creating groups like `iamgroup_yousuf` is foundational for AWS security best practices. Instead of attaching a policy directly to a user, attaching it to a group ensures that anyone added to that group immediately inherits the correct permissions, reducing management overhead and human error.

### 🖥️ Proof of Execution

Below is the AWS CloudTrail event log confirming the successful `CreateGroup` API call, capturing the requested group name, the region endpoint, and the resulting Amazon Resource Name (ARN) generated for the new group.

```json
{
    "eventVersion": "1.11",
    "userIdentity": {
        "type": "IAMUser",
        "accountId": "037671897032",
        "userName": "kk_labs_user_952420"
    },
    "eventTime": "2026-07-31T13:26:27Z",
    "eventSource": "iam.amazonaws.com",
    "eventName": "CreateGroup",
    "awsRegion": "us-east-1",
    "requestParameters": {
        "groupName": "iamgroup_yousuf"
    },
    "responseElements": {
        "group": {
            "path": "/",
            "groupName": "iamgroup_yousuf",
            "groupId": "AGPAQRRLLQ7EHL23N3ICY",
            "arn": "arn:aws:iam::037671897032:group/iamgroup_yousuf",
            "createDate": "2026-07-31T13:26:27Z"
        }
    },
    "eventType": "AwsApiCall",
    "managementEvent": true,
    "eventCategory": "Management"
}

```