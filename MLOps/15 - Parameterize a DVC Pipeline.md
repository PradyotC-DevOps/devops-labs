# 15 - Parameterize a DVC Pipeline

## 📋 Task Overview

<div class="markdown-body text-base">


<meta charset="utf-8">


<p>The xFusionCorp Industries ML team manages model hyperparameters using <code>params.yaml</code>, enabling experiments to be conducted without altering the code. In the fraud-detection project, the <code>train</code> stage retrieves the <code>n_estimators</code> parameter from <code>params.yaml</code>, but this parameter is not declared to DVC, which means that changing its value does not initiate retraining. Integrate the parameter into the pipeline and illustrate the concept of parameter-driven reproducibility.</p>

</div><br><div class="markdown-body text-sm mb-8">


<meta charset="utf-8">


<p>A project exists at <code>/root/code/fraud-detection/</code> with a three-stage DVC pipeline (<code>process_data</code>, <code>split_data</code>, <code>train</code>) and a <code>params.yaml</code> declaring <code>n_estimators: 100</code>. <code>src/models/train.py</code> already reads <code>n_estimators</code> from <code>params.yaml</code>. Do not modify the Python files.</p>
<p>The <code>train</code> stage in <code>dvc.yaml</code> currently has no <code>params:</code> section, so DVC does not track <code>n_estimators</code> — changing it would not re-run the stage.</p>
<p>Acceptance criteria:</p>
<ul>
<li>The <code>train</code> stage in <code>dvc.yaml</code> lists <code>n_estimators</code> under a <code>params:</code> section, and the pipeline has been reproduced.</li>
<li>Parameter-driven retraining is demonstrated: with <code>n_estimators</code> changed to a different value (for example <code>200</code>), re-running the pipeline re-executes only the <code>train</code> stage, records the new value in <code>dvc.lock</code>, and regenerates <code>models/model.pkl</code>.</li>
</ul>
<blockquote>
  <p><code>dvc params diff</code> reports changes to the tracked parameter values across Git commits, which is useful when comparing experiments.</p>
</blockquote>

---

## 🚀 Complete Solution

1. **Update `dvc.yaml` to Track Parameters:**
* Navigated to the project directory `/root/code/fraud-detection/`.
* Edited the `dvc.yaml` file to explicitly declare `n_estimators` as a tracked parameter for the `train` stage.
* *Modified `train` block in `dvc.yaml`:*
```yaml
stages:
  # ... [process_data and split_data stages] ...
  train:
    cmd: python3 src/models/train.py
    deps:
      - data/processed/train.csv
      - src/models/train.py
    params:
      - n_estimators
    outs:
      - models/model.pkl

```




2. **Establish Baseline Execution:**
* Ran `dvc repro` to execute the pipeline with the initial configuration (`n_estimators: 100`). This recorded the baseline parameter hash in the `dvc.lock` file.


3. **Demonstrate Parameter-Driven Retraining:**
* Edited the `params.yaml` file, changing the `n_estimators` value from `100` to `200`.
* Executed `dvc repro` a second time.
* Observed that DVC intelligently skipped the `process_data` and `split_data` stages (since their dependencies had not changed) and *only* re-executed the `train` stage to generate a new `models/model.pkl`.


4. **Compare Experiments:**
* Utilized `dvc params diff` to verify the exact hyperparameter changes tracked by DVC across the workspace.



**💡 Key Learnings & Gotchas (The Git Analogy):**

* **`params:` vs `-d` (Dependencies):** You *could* just list `params.yaml` as a standard dependency (`deps:`). However, by using the `params:` section, DVC parses the YAML file and tracks the *specific variable* (`n_estimators`). If you have 50 parameters in that file but the `train` stage only uses one, changing the other 49 won't trigger an unnecessary retraining!
* **`dvc params diff` is like `git diff` for Data Science:** While `git diff` shows you lines of code that changed, `dvc params diff` shows you a clean table of exactly which machine learning hyperparameters were tweaked between experiments or Git commits.

### 🖥️ Proof of Execution

Below is the terminal trace demonstrating the modification of the parameters, the intelligent caching of the DVC pipeline (skipping untouched stages), and the parameter diffing functionality.

```bash
root@controlplane fraud-detection on  main ➜  dvc repro
Running stage 'process_data':
> python3 src/data/process_data.py
Generating lock file 'dvc.lock'
Running stage 'split_data':
> python3 src/data/split_data.py
Updating lock file 'dvc.lock'
Running stage 'train':
> python3 src/models/train.py
Training model with n_estimators=100
Updating lock file 'dvc.lock'

To track the changes with git, run:
        git add dvc.lock

root@controlplane fraud-detection on  main ➜  nano params.yaml  # Changed n_estimators to 200

root@controlplane fraud-detection on  main ➜  dvc repro
Stage 'process_data' didn't change, skipping
Stage 'split_data' didn't change, skipping
Running stage 'train':
> python3 src/models/train.py
Training model with n_estimators=200
Updating lock file 'dvc.lock'

To track the changes with git, run:
        git add dvc.lock params.yaml

root@controlplane fraud-detection on  main ➜  dvc params diff
Path         Param         Old    New
params.yaml  n_estimators  100    200

root@controlplane fraud-detection on  main ➜  cat dvc.lock
schema: '2.0'
stages:
  process_data:
    # [...]
  split_data:
    # [...]
  train:
    cmd: python3 src/models/train.py
    deps:
    - path: data/processed/train.csv
      hash: md5
      md5: 142467e5074926d5eb5e7154aa456c25
      size: 441
    - path: src/models/train.py
      hash: md5
      md5: 30cd8f3450b18ac68b88b4c42f2620e4
      size: 664
    params:
      params.yaml:
        n_estimators: 200
    outs:
    - path: models/model.pkl
      hash: md5
      md5: 1581718034523a4987a58c81b89468fe
      size: 139097

```