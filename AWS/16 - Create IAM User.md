# 16 - Create IAM User

## 📋 Task Overview

<div class="flex flex-col"><!----><div class="markdown-body text-base">


<meta charset="utf-8">


<p>When establishing infrastructure on the AWS cloud, Identity and Access Management (IAM) is among the first and most critical services to configure. IAM facilitates the creation and management of user accounts, groups, roles, policies, and other access controls. The Nautilus DevOps team is currently in the process of configuring these resources and has outlined the following requirements:</p>
<p>For this task, create an IAM user named <code>iamuser_javed</code>.</p>

</div>

---

## 🚀 Complete Solution

1. **Access AWS IAM Dashboard:**
* Logged into the AWS Management Console using the provided lab administrative credentials.
* Navigated to the **IAM (Identity and Access Management)** service.


2. **Provision New User:**
* Selected **Users** from the left-hand navigation pane and clicked **Add users**.
* Entered the required username: `iamuser_javed`.
* Proceeded through the permissions configuration (leaving it blank for now as the task strictly required user creation, not policy attachment).


3. **Review and Create:**
* Reviewed the configuration details and clicked **Create user**.
* Verified the user successfully appeared in the IAM Users list.



**💡 Key Learnings & Gotchas:**

* **CloudTrail Auditing:** Every action taken in the AWS Management Console triggers an API call behind the scenes. Knowing how to find these `CreateUser` events in AWS CloudTrail is essential for security auditing and tracking who created what resources.
* **Global Resources:** Unlike EC2 instances or VPCs, IAM users are global resources. They are not restricted to a specific region (like `us-east-1`), which is why IAM configurations apply universally across your AWS environment.

### 🖥️ Proof of Execution

Below is the AWS CloudTrail event log confirming the successful `CreateUser` API call, capturing the exact timestamp, requested username, and the resulting Amazon Resource Name (ARN) generated for the new user.

```json
{
    "eventVersion": "1.11",
    "userIdentity": {
        "type": "IAMUser",
        "principalId": "AIDASXZNZPAUQJH5AYNGH",
        "arn": "arn:aws:iam::188537731113:user/kk_labs_user_571112",
        "accountId": "188537731113",
        "userName": "kk_labs_user_571112"
    },
    "eventTime": "2026-07-30T18:56:14Z",
    "eventSource": "iam.amazonaws.com",
    "eventName": "CreateUser",
    "awsRegion": "us-east-1",
    "requestParameters": {
        "userName": "iamuser_javed"
    },
    "responseElements": {
        "user": {
            "path": "/",
            "userName": "iamuser_javed",
            "userId": "AIDASXZNZPAUYGPFQFRDJ",
            "arn": "arn:aws:iam::188537731113:user/iamuser_javed",
            "createDate": "2026-07-30T18:56:14Z"
        }
    },
    "eventType": "AwsApiCall",
    "managementEvent": true,
    "eventCategory": "Management"
}

```