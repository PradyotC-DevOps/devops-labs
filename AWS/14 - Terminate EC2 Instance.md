# 14 - Terminate EC2 Instance

## 📋 Task Overview

During the migration process, several resources were created under the AWS account. Later on, some of these resources became obsolete as alternative solutions were implemented. Similarly, there is an instance that needs to be deleted as it is no longer in use.

1. Delete the EC2 instance named `datacenter-ec2` present in the `us-east-1` region.
2. Before submitting your task, make sure the instance is in the `terminated` state.

`Notes:`

   - Create the resources only in `us-east-1` region.

---

## 🚀 Complete Solution

### Execution Steps

Here is the standard procedure to terminate the obsolete EC2 instance using the AWS Management Console:

1. **Navigate to the EC2 Dashboard:**
Log in to the AWS Management Console and ensure your active region is set to **N. Virginia (`us-east-1`)** in the top-right corner. Search for **EC2** and open the dashboard.


2. **Locate the Target Instance:**
1. On the left-hand navigation pane, click on **Instances**.
2. In the search bar, filter by the instance name: **`datacenter-ec2`**.
3. Select the instance by checking the box next to its name.


3. **Terminate the Instance:** This action is irreversible and permanently deletes any data on attached ephemeral/root volumes.
1. Click the **Instance state** dropdown menu at the top right.
2. Select **Terminate instance**.
3. A warning prompt will appear. Confirm the deletion by clicking **Terminate**.


4. **Verify the Terminated State:** Fulfills requirement #2.
The instance state will initially change to `shutting-down`. Wait a few moments and refresh the console until the state changes completely to **`terminated`**. Once confirmed, the task is complete.


---

## 📊 Verification and Operation Logs

Below is the sanitized AWS CloudTrail event log confirming the successful execution of the `TerminateInstances` API call.

*Note: Sensitive IAM credentials, account IDs, IP addresses, and verbose metadata have been redacted or folded for security and brevity.*

```json
{
    "eventVersion": "1.11",
    "eventTime": "2026-07-24T08:00:21Z",
    "eventSource": "ec2.amazonaws.com",
    "eventName": "TerminateInstances",
    "awsRegion": "us-east-1",
    "userIdentity": {
        "type": "IAMUser",
        "principalId": "[REDACTED_PRINCIPAL_ID]",
        "arn": "arn:aws:iam::[REDACTED_ACCOUNT_ID]:user/[REDACTED_USER]",
        "accountId": "[REDACTED_ACCOUNT_ID]",
        "accessKeyId": "[REDACTED_ACCESS_KEY]"
    },
    "requestParameters": {
        "instancesSet": {
            "items": [
                {
                    "instanceId": "i-004278a1f932f4140"
                }
            ]
        },
        "force": false
    },
    "responseElements": {
        "instancesSet": {
            "items": [
                {
                    "instanceId": "i-004278a1f932f4140",
                    "currentState": {
                        "code": 32,
                        "name": "shutting-down"
                    },
                    "previousState": {
                        "code": 16,
                        "name": "running"
                    }
                }
            ]
        }
    },
    "eventType": "AwsApiCall",
    "eventCategory": "Management",
    "note": "[REDACTED] - User agent, source IP, TLS details, and other metadata omitted for security and brevity."
}

```