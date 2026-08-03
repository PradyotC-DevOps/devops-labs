# 19 - Attach IAM Policy to IAM User

## 📋 Task Overview

<div class="flex flex-col"><!----><div class="markdown-body text-base">


<meta charset="utf-8">


<p>The Nautilus DevOps team has been creating a couple of services on AWS cloud. They have been breaking down the migration into smaller tasks, allowing for better control, risk mitigation, and optimization of resources throughout the migration process. Recently they came up with requirements mentioned below.</p>
<p>An IAM user named <code>iamuser_james</code> and a policy named <code>iampolicy_james</code> already exist. Attach the IAM policy <code>iampolicy_james</code> to the IAM user <code>iamuser_james</code>.</p>

</div>

---

## 🚀 Complete Solution

1. **Access the IAM Dashboard:**
* Logged into the AWS Management Console and navigated to the **IAM (Identity and Access Management)** service.


2. **Locate the IAM User:**
* Selected **Users** from the left-hand navigation pane.
* Searched for and selected the target user: `iamuser_james`.


3. **Attach the Policy:**
* Under the **Permissions** tab, clicked on **Add permissions** and selected **Add permissions**.
* Chose the **Attach policies directly** option.
* Filtered the list of policies for the customer-managed policy named `iampolicy_james`.
* Selected the policy and clicked **Add permissions** to finalize the attachment.


4. **Validation:**
* Verified that `iampolicy_james` appeared under the active Permissions policies list for the user `iamuser_james`.



**💡 Key Learnings & Gotchas:**

* **Direct Attachment vs. Group Attachment:** While AWS allows you to attach policies directly to individual IAM users (as done in this task), AWS best practices generally recommend attaching policies to **IAM Groups** or mapping them via **IAM Roles**. Direct attachment is useful for exceptions or highly specific service accounts, but group-based Role-Based Access Control (RBAC) is easier to scale.
* **`responseElements: null`:** In AWS CloudTrail, an API call like `AttachUserPolicy` returning `null` for `responseElements` actually indicates a successful execution. If it had failed, there would be an `errorCode` and `errorMessage` block instead!

### 🖥️ Proof of Execution

Below is the AWS CloudTrail event log confirming the successful `AttachUserPolicy` API call. It cleanly captures the exact policy ARN being mapped to the target user.

```json
{
    "eventVersion": "1.11",
    "userIdentity": {
        "type": "IAMUser",
        "accountId": "417975622584",
        "userName": "kk_labs_user_330431"
    },
    "eventTime": "2026-08-03T11:20:19Z",
    "eventSource": "iam.amazonaws.com",
    "eventName": "AttachUserPolicy",
    "awsRegion": "us-east-1",
    "requestParameters": {
        "userName": "iamuser_james",
        "policyArn": "arn:aws:iam::417975622584:policy/iampolicy_james"
    },
    "responseElements": null,
    "eventType": "AwsApiCall",
    "managementEvent": true,
    "eventCategory": "Management"
}

```