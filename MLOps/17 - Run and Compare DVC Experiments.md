# 17 - Run and Compare DVC Experiments

## 📋 Task Overview

<div class="px-12 pb-4"><!----><div class="mt-4"><!----><div class="flex flex-col"><!----><div class="markdown-body text-base">


<meta charset="utf-8">


<p>The xFusionCorp Industries MLOps team needs every model training run to be reproducible, automatically tracked, and easy to compare so a chosen configuration can be promoted into version control. The fraud-detection pipeline is parameterized by <code>max_depth</code>, currently set shallow enough to underfit. Using DVC experiments, run three tracked experiments over different <code>max_depth</code> values, compare their recorded <code>f1_score</code> on the held-out test set, and promote the best-scoring run so its parameters, metrics, and model become the tracked workspace state.</p>

</div><br><div class="markdown-body text-sm mb-8">


<meta charset="utf-8">


<p>A project exists at <code>/root/code/fraud-detection/</code> with a parameterised DVC pipeline already in place. <code>params.yaml</code> declares <code>n_estimators: 100</code> and <code>max_depth: 4</code>, and the baseline pipeline has been run once. <code>src/models/train.py</code> reads both parameters, trains the model, and evaluates it on the held-out test set, writing the real <code>accuracy</code> and <code>f1_score</code> to <code>metrics.json</code>. Do not modify the Python files.</p>
<p>Acceptance criteria:</p>
<ul>
<li>Three DVC experiments have been run, each with a different value for <code>max_depth</code> across a reasonable range (for example <code>2</code>, <code>6</code>, and <code>12</code>); each experiment retrains the model and produces a fresh <code>metrics.json</code>.</li>
<li>The experiment with the highest <code>f1_score</code> is applied to the workspace, so its <code>max_depth</code>, <code>metrics.json</code>, and <code>models/model.pkl</code> become the tracked state.</li>
</ul>
<blockquote>
  <p>The DVC extension's <strong>EXPERIMENTS</strong> view (open the DVC panel from the Activity Bar) lists every experiment alongside its parameters and metrics, which is a convenient way to compare runs at a glance.</p>
</blockquote>

</div>

---

## 🚀 Complete Solution

1. **Run Tracked Experiments via CLI:**
* Navigated to `/root/code/fraud-detection/`.
* Used the `dvc exp run --set-param` (or `-S`) command to dynamically override the `max_depth` parameter defined in `params.yaml` and trigger a pipeline reproduction.
* Executed three distinct experiments to test a reasonable range of tree depths:
```bash
dvc exp run -S max_depth=2
dvc exp run -S max_depth=6
dvc exp run -S max_depth=12

```


* *Note: DVC intelligently reused cached data for the `process_data` and `split_data` stages, only re-running the `train` stage for each experiment.*


2. **Compare Experiment Metrics:**
* Ran `dvc exp show` to view a comprehensive, tabulated breakdown of all recent experiments, plotting their parameters (`max_depth`, `n_estimators`) against their outputs (`accuracy`, `f1_score`).
* Identified that the experiment utilizing `max_depth=12` yielded the highest `f1_score` without heavily overfitting.


3. **Promote the Best Experiment:**
* Extracted the auto-generated experiment name (e.g., `beaten-sake`) for the run with `max_depth=12`.
* Executed `dvc exp apply` to apply the experiment's specific parameters, metrics, and generated model (`models/model.pkl`) to the current workspace.
```bash
dvc exp apply beaten-sake

```


* Tracked the newly promoted `dvc.lock` and `params.yaml` files via Git.



**💡 Key Learnings & Gotchas (The Git Analogy):**

* **Dynamic Parameter Overrides (`-S`):** Using `dvc exp run -S <param>=<value>` is incredibly powerful. It temporarily rewrites your `params.yaml` file, runs the pipeline, and records the results automatically. You don't have to manually open a text editor for every hyperparameter tweak.
* **Experiments vs. Git Branches:** In traditional software engineering, you create a Git branch for every new feature. In Data Science, testing 50 hyperparameters would mean creating 50 Git branches, which is a nightmare to manage. DVC Experiments solve this by acting as hidden, lightweight, temporary Git commits. You can run hundreds of experiments, compare them, and only "apply" (merge) the single best one to your actual working directory.

### 🖥️ Proof of Execution

Below is the terminal trace demonstrating the dynamic execution of multiple parameterized experiments, the tabular comparison of their metrics, and the successful promotion of the best-performing run to the workspace.

```bash
root@controlplane fraud-detection on  main ➜  dvc exp run -S max_depth=2
dvc exp run -S max_depth=6
dvc exp run -S max_depth=12
Reproducing experiment 'strip-goos'
Stage 'process_data' didn't change, skipping                
Stage 'split_data' didn't change, skipping                  
Running stage 'train':                                      
> python3 src/models/train.py
max_depth=2, n_estimators=100, metrics={'accuracy': 0.6, 'f1_score': 0.2453}
Updating lock file 'dvc.lock'                               

Ran experiment(s): strip-goos
Experiment results have been applied to your workspace.

Reproducing experiment 'azoic-bean'
Stage 'process_data' didn't change, skipping                
Stage 'split_data' didn't change, skipping                  
Running stage 'train':                                      
> python3 src/models/train.py
max_depth=6, n_estimators=100, metrics={'accuracy': 0.745, 'f1_score': 0.6483}
Updating lock file 'dvc.lock'                               

Ran experiment(s): azoic-bean
Experiment results have been applied to your workspace.

Reproducing experiment 'still-dawk'
Stage 'process_data' didn't change, skipping                
Stage 'split_data' didn't change, skipping                  
Running stage 'train':                                      
> python3 src/models/train.py
max_depth=12, n_estimators=100, metrics={'accuracy': 0.75, 'f1_score': 0.6795}
Updating lock file 'dvc.lock'                               

Ran experiment(s): still-dawk
Experiment results have been applied to your workspace.

root@controlplane fraud-detection on  main [!] ➜  dvc exp apply still-dawk
Changes for experiment 'still-dawk' have been applied to your current workspace.

```