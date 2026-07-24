# 12 - Fix a Broken DVC Remote and Push to SeaweedFS

## 📋 Task Overview

The xFusionCorp Industries ML team uses SeaweedFS as the shared S3-compatible object store for DVC-tracked data. A `.dvc/config` already declares a remote called `s3` for the fraud-detection project, but `dvc push` currently fails. Correct the configuration and push the tracked data into the SeaweedFS bucket.

A project exists at `/root/code/fraud-detection/` with DVC initialised and `data/raw/transactions.csv` already tracked.

SeaweedFS is already running on the controlplane:

* **S3 endpoint:** `http://localhost:8333`
* **Filer UI:** open the **SeaweedFS Filer** button at the top of the lab (forwarded port 8888) – buckets are visible under `/buckets/`.
* **Credentials:** `weedadmin` / `weedadmin123` (already set in `.dvc/config`)
* **Bucket name:** `dvc-storage` (already created and visible in the Filer UI under `/buckets/dvc-storage`)

Run `dvc push` to see it fail, then inspect `.dvc/config` against the endpoint, bucket, and credentials above.

**Acceptance criteria:**

* The remote called `s3` points at the `dvc-storage` bucket using `s3://`, uses the correct SeaweedFS S3 endpoint URL, and is marked as the default remote.
* After the push, the **dvc-storage** bucket in the SeaweedFS Filer UI contains at least one object under the `files/md5/...` prefix.

---

## 🚀 Complete Solution

### Step 1: Initial Discovery and Workspace Verification

Before modifying any configurations, it is a crucial DevOps best practice to map out the current state of the workspace. By inspecting the directory structure, we can confirm that Git and DVC are properly initialized and that the dataset is successfully cached in the local DVC state.

First, navigate to the target repository:

```bash
cd /root/code/fraud-detection

```

Next, use the `tree -a` command (or `ls -laR`) to expose the entire repository structure, including hidden configuration and caching directories:

```bash
tree -a

```

**Key Takeaways from the Verification Output:**

1. **`.git/` presence:** Confirms the repository is under standard source control.
2. **`data/raw/transactions.csv.dvc`:** Confirms the dataset is being actively tracked by DVC via a pointer file, rather than being tracked by Git.
3. **`.dvc/cache/files/md5/82/564f01ab73aa3860e11327cc14be2c`:** This is the most critical discovery. It proves that DVC has already hashed the `transactions.csv` file locally and stored the raw data payload in its local cache. This MD5 hash path is exactly what DVC will attempt to replicate to the SeaweedFS remote.

### Step 2: Diagnosing and Patching the DVC Configuration

By default, DVC assumes an `s3://` URL points to Amazon Web Services (AWS). Because the ML team uses SeaweedFS hosted on the local controlplane, the remote configuration must be explicitly redirected.

We apply three targeted patches to the DVC configuration using the `dvc remote modify` command:

**1. Define the correct storage bucket:**

```bash
dvc remote modify s3 url s3://dvc-storage

```

*Explanation: This updates the remote URL to utilize the S3 protocol and sets the target bucket strictly to `dvc-storage`, matching the bucket provisioned by the SeaweedFS Filer.*

**2. Override the default AWS endpoint:**

```bash
dvc remote modify s3 endpointurl http://localhost:8333

```

*Explanation: This is the critical fix. It redirects the underlying boto3/S3 client DVC uses to point at the local SeaweedFS S3 API gateway (`localhost:8333`) instead of attempting to resolve `s3.amazonaws.com`.*

**3. Set as the default remote:**

```bash
dvc remote default s3

```

*Explanation: Marking this remote as the default ensures that any subsequent `dvc push`, `dvc pull`, or `dvc fetch` commands will automatically route to SeaweedFS without requiring the `--remote s3` flag.*

### Step 3: Pushing the Dataset to the Object Store

With the configuration correctly routing traffic to the SeaweedFS endpoint, execute the push operation to sync the local cache with the remote bucket.

```bash
dvc push

```

**Expected Output:**

```text
Collecting                                                  |1.00 [00:00,  711entry/s]
Pushing
1 file pushed

```

*Explanation: DVC scans the local pointer files, identifies the cached MD5 block (`82/564f01...`), authenticates using the pre-configured `weedadmin` credentials, and successfully streams the object to SeaweedFS.*

### Step 4: Programmatic Validation and Verification

While standard Linux commands like `tree` or `ls` cannot natively read the contents of an S3-compatible object store directory, SeaweedFS exposes a RESTful Filer UI on port 8888. We can use `curl` to directly query the Filer's API and verify the exact data payload.

To prove the dataset was uploaded and properly structured under the DVC MD5 schema, query the Filer endpoint using the hash discovered during Step 1:

```bash
curl http://localhost:8888/buckets/dvc-storage/files/md5/82/564f01ab73aa3860e11327cc14be2c

```

**Verification Output:**

```text
transaction_id,amount,merchant,category,is_fraud
1001,25.50,StoreA,groceries,0
1002,1250.00,OnlineShopB,electronics,1
1003,45.00,RestaurantC,dining,0
1004,890.00,StoreD,clothing,0
1005,3200.00,OnlineShopE,electronics,1

```

### ✅ Conclusion

The raw CSV payload successfully outputs to the terminal, confirming that the DVC cache was perfectly replicated to the SeaweedFS `dvc-storage` bucket. The remote is now permanently fixed, and the ML team can safely push and pull versioned datasets.