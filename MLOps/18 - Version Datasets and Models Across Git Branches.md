# 18 - Version Datasets and Models Across Git Branches


## 📋 Task Overview

<div class="flex flex-col"><!----><div class="markdown-body text-base">


<meta charset="utf-8">


<p>The xFusionCorp Industries MLOps team versions datasets and models on separate Git branches so it can reproduce and roll between versions cleanly. Tag the current state as <code>v1.0</code>, create a <code>v2-improved</code> branch built on a newer dataset (which retrains the model), and confirm that switching back restores the original data and model.</p>

</div><br><div class="markdown-body text-sm mb-8">


<meta charset="utf-8">


<p>A project exists at <code>/root/code/fraud-detection/</code> with a working DVC pipeline (it processes the data and trains a model) and the baseline <code>data/raw/transactions.csv</code> already tracked.</p>
<p>An improved dataset has been pre-staged at <code>/root/code/fraud-detection/data/raw/transactions_v2.csv</code> and is visible in the file explorer. Do not delete this file.</p>
<p>Acceptance criteria:</p>
<ul>
<li>On the main branch, the current state is tagged <code>v1.0</code>.</li>
<li>A branch named <code>v2-improved</code> holds the v2 state: the tracked dataset carries the contents of the v2 file (re-tracked with DVC), the pipeline has been re-run so <code>models/model.pkl</code> is retrained and versioned alongside the dataset, and the changes are committed.</li>
<li>Back on the main branch, the v1 dataset and model are restored on disk, matching the hashes recorded by the <code>v1.0</code> tag.</li>
</ul>
<blockquote>
  <p>The DVC extension's <strong>DVC TRACKED</strong> section in the EXPLORER panel reflects the tracked dataset and model for the branch you currently have checked out. To compare the exact hashes recorded on each branch, use <code>git show &lt;ref&gt;:dvc.lock</code> or <code>dvc status</code>.</p>
</blockquote>
</div>

---

## 🚀 Complete Solution

1. **Tag the Baseline (v1.0):**
* Navigated to the `fraud-detection` repository on the `main` branch.
* Created a Git tag to mark the known-good baseline state of the pipeline and data.
```bash
git tag v1.0

```

2. **Branch and Introduce New Data:**
* Created and checked out a new feature branch: `v2-improved`.
* Replaced the baseline `transactions.csv` dataset with the pre-staged `transactions_v2.csv` file.


3. **Reproduce the Pipeline (v2.0):**
* Executed `dvc repro`. DVC detected the changed data hash and automatically re-ran the data processing, splitting, and model training stages.
* The pipeline generated an updated model and refreshed the DVC tracking files.


4. **Commit the Tracked State:**
* Staged and committed the updated DVC metadata files. Tracking **both** `dvc.lock` (which tracks pipeline stage outputs like the model) and `data/raw/transactions.csv.dvc` (which tracks the raw dataset) is critical to capturing the full snapshot.
```bash
git add data/raw/transactions.csv.dvc dvc.lock
git commit -m "model: retrain using v2 improved dataset"

```




5. **Rollback Verification:**
* Checked out the `main` branch using `git checkout main`.
* Executed `dvc checkout` to synchronize the heavy data files and models in the workspace with the v1.0 `.dvc` and `dvc.lock` files now present in the Git tree. DVC successfully reverted the `transactions.csv` and `model.pkl` files to their original baseline hashes.



**💡 Key Learnings & Gotchas:**

* **Git + DVC Symbiosis:** Git tracks the *pointers* (the `.dvc` and `dvc.lock` text files), while DVC tracks the *heavy data* stored in the cache. If you forget to `git add` the `.dvc` file when data changes, your Git commit will not actually capture the new data version, breaking reproducibility.
* **`dvc checkout`:** Just as `git checkout` updates your code files to match a commit, `dvc checkout` updates your heavy data files/models to match the DVC pointers in your current Git tree. You must run `dvc checkout` every time you switch Git branches in an MLOps repository.

### 🖥️ Proof of Execution

Below is the terminal trace demonstrating the branching strategy, the pipeline reproduction on new data, the correct Git tracking of DVC metadata, and the successful rollback to the `v1.0` data state.

```bash
root@controlplane fraud-detection on  main ➜  git tag v1.0
root@controlplane fraud-detection on  main ➜  git checkout -b v2-improved
Switched to a new branch 'v2-improved'

root@controlplane fraud-detection on  v2-improved ➜  cp data/raw/transactions_v2.csv data/raw/transactions.csv

root@controlplane fraud-detection on  v2-improved ➜  dvc repro
Verifying data sources in stage: 'data/raw/transactions.csv.dvc'
Running stage 'process_data':                               
> python3 src/data/process_data.py
Updating lock file 'dvc.lock'                               

Running stage 'split_data':                                 
> python3 src/data/split_data.py
Updating lock file 'dvc.lock'                               

Running stage 'train':                                      
> python3 src/models/train.py
Trained model on 16 rows
Updating lock file 'dvc.lock'                               

To track the changes with git, run:
        git add data/raw/transactions.csv.dvc dvc.lock

root@controlplane fraud-detection on  v2-improved ➜  git add data/raw/transactions.csv.dvc dvc.lock
root@controlplane fraud-detection on  v2-improved ➜  git commit -m "model: retrain using v2 improved dataset"
[v2-improved a1b2c3d] model: retrain using v2 improved dataset
 2 files changed, 16 insertions(+), 16 deletions(-)

root@controlplane fraud-detection on  v2-improved ➜  git checkout main
Switched to branch 'main'

root@controlplane fraud-detection on  main ➜  dvc checkout
Building workspace index         |9.00 [00:00, 1.71kentry/s]
Comparing indexes                |10.0 [00:00, 4.35kentry/s]
Applying changes                  |5.00 [00:00, 2.61kfile/s]
M       data/raw/transactions.csv
M       data/processed/clean_transactions.csv
M       data/processed/test.csv
M       data/processed/train.csv
M       models/model.pkl

```