# 15 - Create Volume Snapshot

## 📋 Task Overview

<div class="flex flex-col"><!----><div class="markdown-body text-base">


<meta charset="utf-8">


<p>The Nautilus DevOps team has some volumes in different regions in their AWS account. They are going to setup some automated backups so that all important data can be backed up on regular basis. For now they shared some requirements to take a snapshot of one of the volumes they have.</p>
<p>Create a snapshot of an existing volume named <code>nautilus-vol</code>  in <code>us-east-1</code> region.</p>
<p>1) The name of the snapshot must be <code>nautilus-vol-ss</code>.</p>
<p>2) The description must be <code>nautilus Snapshot</code>.</p>
<p>3) Make sure the snapshot status is <code>completed</code> before submitting the task.</p>

</div>

<p><code>Notes:</code> </p>
<ul>
<li><p>Create the resources only in <code>us-east-1</code> region.</p></li>
</ul>

---

## 🚀 Complete Solution

### 1. Locate the Volume and Create Snapshot
*   Navigated to the **EC2 Dashboard** > **Volumes** in the `us-east-1` region.
*   Located the volume named `nautilus-vol` (Volume ID: `vol-***`).
*   Selected the volume, clicked **Actions** > **Create Snapshot**.
*   Entered the description `nautilus Snapshot` and initiated the creation.

### 2. Apply Tags to the Snapshot
*   Navigated to the **Snapshots** dashboard.
*   Selected the newly created snapshot (`snap-***`).
*   Under the **Tags** tab, added a new tag with Key `Name` and Value `nautilus-vol-ss`.

### 3. Verification & CloudTrail Audit Logs
Waited for the snapshot status to change to `completed` in the console UI. 

Below are the scrubbed CloudTrail event logs confirming the successful API calls triggered by the Web Console actions:

<details>
<summary><strong>View CloudTrail Event: CreateSnapshot</strong></summary>

```json
{
    "eventVersion": "1.11",
    "userIdentity": {
        "type": "IAMUser",
        "principalId": "<REDACTED>",
        "arn": "arn:aws:iam::<REDACTED_ACCOUNT_ID>:user/<REDACTED_USER>",
        "accountId": "<REDACTED_ACCOUNT_ID>",
        "accessKeyId": "<REDACTED_ACCESS_KEY>",
        "userName": "<REDACTED_USER>"
    },
    "eventTime": "2026-07-25T08:24:24Z",
    "eventName": "CreateSnapshot",
    "awsRegion": "us-east-1",
    "sourceIPAddress": "<REDACTED_IP>",
    "requestParameters": {
        "volumeId": "vol-<REDACTED>",
        "description": "nautilus Snapshot"
    },
    "responseElements": {
        "snapshotId": "snap-<REDACTED>",
        "volumeId": "vol-<REDACTED>",
        "status": "pending",
        "description": "nautilus Snapshot"
    },
    "eventType": "AwsApiCall"
}

```

```json
{
    "eventVersion": "1.11",
    "userIdentity": {
        "type": "IAMUser",
        "principalId": "<REDACTED>",
        "arn": "arn:aws:iam::<REDACTED_ACCOUNT_ID>:user/<REDACTED_USER>",
        "accountId": "<REDACTED_ACCOUNT_ID>",
        "accessKeyId": "<REDACTED_ACCESS_KEY>",
        "userName": "<REDACTED_USER>"
    },
    "eventTime": "2026-07-25T08:24:44Z",
    "eventName": "CreateTags",
    "awsRegion": "us-east-1",
    "sourceIPAddress": "<REDACTED_IP>",
    "requestParameters": {
        "resourcesSet": {
            "items": [
                {
                    "resourceId": "snap-<REDACTED>"
                }
            ]
        },
        "tagSet": {
            "items": [
                {
                    "key": "Name",
                    "value": "nautilus-vol-ss"
                }
            ]
        }
    },
    "responseElements": {
        "_return": true
    },
    "eventType": "AwsApiCall"
}

```

## ✅ Conclusion

Successfully located the target EBS volume, triggered a snapshot with the requested description via the AWS Web Console, explicitly tagged it with the required name, and ensured the backup reached a fully completed state in the `us-east-1` region.