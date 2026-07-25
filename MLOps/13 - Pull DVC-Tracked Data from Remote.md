## 13 - Pull DVC-Tracked Data from Remote

## 📋 Task Overview

<div class="mt-4"><!----><div class="flex flex-col"><!----><div class="markdown-body text-base">


<meta charset="utf-8">


<p>A new team member at xFusionCorp Industries has cloned the fraud-detection repository onto a fresh machine. Although the DVC remote is correctly configured to point to the team's SeaweedFS bucket, the <code>dvc pull</code> command is failing. Your task is to diagnose the cause of this failure, correct the configuration as needed, and successfully pull the dataset.</p>

</div><br><div class="markdown-body text-sm mb-8">


<meta charset="utf-8">


<p>A cloned project exists at <code>/root/code/fraud-detection/</code> with DVC initialised and the <code>data/raw/transactions.csv.dvc</code> pointer file present, but the dataset itself is missing from disk and from the local DVC cache.</p>
<p>SeaweedFS is already running on the controlplane and the dataset has already been pushed to the <strong>dvc-storage</strong> bucket — open the <strong>SeaweedFS Filer</strong> button at the top of the lab and navigate to <code>/buckets/dvc-storage/</code> to confirm the object is there.</p>
<ul>
<li><strong>S3 endpoint:</strong> <code>http://localhost:8333</code></li>
<li><strong>Credentials:</strong> <code>weedadmin</code> / <code>weedadmin123</code></li>
</ul>
<p>Run <code>dvc pull</code> to see it fail, then inspect <code>.dvc/config</code> against the endpoint and credentials above.</p>
<p>Acceptance criteria:</p>
<ul>
<li>The <code>s3</code> remote in <code>.dvc/config</code> reaches SeaweedFS with the access key (<code>access_key_id</code>) <code>weedadmin</code> and the secret key (<code>secret_access_key</code>) <code>weedadmin123</code>.</li>
<li>After the pull, <code>data/raw/transactions.csv</code> is present on disk and its content matches the hash recorded in the <code>.dvc</code> pointer.</li>
</ul>

---

## 🚀 Complete Solution

### 1. Navigate to the Project Directory
```bash
cd ~/code/fraud-detection/

```

### 2. Update DVC Remote Configuration

The local clone's DVC remote (named `s3`) lacked the correct endpoint URL and credentials to authenticate with the SeaweedFS bucket. These were added using `dvc remote modify`:

```bash
# Point the remote to the local SeaweedFS S3 endpoint
dvc remote modify s3 endpointurl http://localhost:8333

# Set the Access Key ID
dvc remote modify s3 access_key_id weedadmin

# Set the Secret Access Key
dvc remote modify s3 secret_access_key weedadmin123

```

### 3. Verify the Configuration

Checked `.dvc/config` to ensure the new parameters were correctly applied:

```bash
cat .dvc/config

```

**Output:**

```ini
[core]
    remote = s3
['remote "s3"']
    url = s3://dvc-storage
    endpointurl = http://localhost:8333
    access_key_id = weedadmin
    secret_access_key = weedadmin123

```

### 4. Pull the Dataset

With the remote successfully configured, pulled the tracked data from the S3 bucket:

```bash
dvc pull

```

**Output:**

```text
Collecting                                                                        |1.00 [00:00,  784entry/s]
Fetching
Building workspace index                                                          |2.00 [00:00,  747entry/s]
Comparing indexes                                                                 |4.00 [00:00, 3.20kentry/s]
Applying changes                                                                  |1.00 [00:00, 1.31kfile/s]
A       data/raw/transactions.csv
1 file fetched and 1 file added

```

### 5. Verify the Restored Data

Confirmed the file was successfully downloaded and its contents matched the expected hash:

```bash
cat data/raw/transactions.csv

```

**Output:**

```csv
transaction_id,amount,merchant,category,is_fraud
1001,25.50,StoreA,groceries,0
1002,1250.00,OnlineShopB,electronics,1
1003,45.00,RestaurantC,dining,0
1004,890.00,StoreD,clothing,0
1005,3200.00,OnlineShopE,electronics,1
1006,12.99,CafeF,dining,0
1007,560.00,StoreG,clothing,0
1008,78.50,GroceryH,groceries,0
1009,4100.00,OnlineShopI,electronics,1
1010,33.00,RestaurantJ,dining,0
1011,2750.00,OnlineShopK,electronics,1
1012,19.99,StoreL,groceries,0

```