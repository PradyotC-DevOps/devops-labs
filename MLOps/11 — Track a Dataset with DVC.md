# 11 — Track a Dataset with DVC

## 📋 Task Overview

A teammate has added the transactions dataset to the xFusionCorp Industries `fraud-detection` repository. However, it was committed directly to Git rather than being tracked with DVC. Your task is to align the repository with the team standard, ensuring that every dataset under the `data/` directory is tracked by DVC instead of Git.


A project exists at `/root/code/fraud-detection/` with DVC already initialised. The dataset `data/raw/transactions.csv` is currently tracked by Git, and the team standard requires DVC to own it instead.

Acceptance criteria:
   - Git no longer tracks the dataset, but the file remains on disk.
   - The dataset is tracked by DVC instead: a `.dvc` pointer file exists and `data/raw/.gitignore` excludes the dataset itself.
   - The new `.dvc` pointer and `.gitignore` are recorded in a Git commit with the message `Track transactions dataset with DVC`.
   - Once tracking is moved to DVC, the DVC TRACKED section in the EXPLORER panel will list the dataset, confirming the extension recognises it as a DVC-managed file.


> Once tracking is moved to DVC, the DVC TRACKED section in the EXPLORER panel will list the dataset, confirming the extension recognises it as a DVC-managed file.

---

## 🚀 Complete Solution

### Step 1: Navigate to Project Repository & Check Structure

First, navigate to the `fraud-detection` repository directory.

```bash
cd /root/code/fraud-detection
tree
```

**Terminal Output:**

```text
root@controlplane ~/code ➜  tree
.
├── README.md
└── fraud-detection
    ├── README.md
    └── data
        └── raw
            └── transactions.csv

4 directories, 3 files

root@controlplane ~/code ✖ cd fraud-detection/
root@controlplane fraud-detection on  main ➜  
```

---

### Step 2: Remove Dataset from Git Tracking

Remove `data/raw/transactions.csv` from the Git index while keeping the physical file on disk using `--cached`:

```bash
git rm --cached data/raw/transactions.csv
```

**Terminal Output:**

```text
root@controlplane fraud-detection on  main ➜  git rm --cached data/raw/transactions.csv
rm 'data/raw/transactions.csv'
```

---

### Step 3: Add Dataset to DVC

Track the raw dataset using DVC:

```bash
dvc add data/raw/transactions.csv
```

**Terminal Output:**

```text
root@controlplane fraud-detection on  main [✘?] ➜  dvc add data/raw/transactions.csv
100% Adding...|████████████████████|1/1 [00:00, 24.53file/s]
                                                            
To track the changes with git, run:

        git add data/raw/.gitignore data/raw/transactions.csv.dvc

To enable auto staging, run:

        dvc config core.autostage true
```

---

### Step 4: Stage & Commit Changes to Git

Stage the `.dvc` pointer file and the updated `.gitignore`, then commit with the specified message:

```bash
# Stage DVC pointer and gitignore files
git add data/raw/transactions.csv.dvc data/raw/.gitignore

# Commit changes with required message
git commit -m "Track transactions dataset with DVC"
```

**Terminal Output:**

```text
root@controlplane fraud-detection on  main [✘?] ➜  git add data/raw/transactions.csv.dvc data/raw/.gitignore
root@controlplane fraud-detection on  main [✘?] ➜  git commit -m "Track transactions dataset with DVC"
[main ebbdb9a] Track transactions dataset with DVC
 3 files changed, 6 insertions(+), 11 deletions(-)
 create mode 100644 data/raw/.gitignore
 delete mode 100644 data/raw/transactions.csv
 create mode 100644 data/raw/transactions.csv.dvc
```

---

### Step 5: Verification

Verify that both Git and DVC working trees are clean and up to date:

```bash
git status
dvc status
```

**Terminal Output:**

```text
root@controlplane fraud-detection on  main ➜  git status
On branch main
nothing to commit, working tree clean

root@controlplane fraud-detection on  main ➜  dvc status
Data and pipelines are up to date.
```
