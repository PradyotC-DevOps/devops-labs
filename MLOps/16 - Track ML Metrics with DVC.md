# 16 - Track ML Metrics with DVC

## 📋 Task Overview

<div class="markdown-body text-base">


<meta charset="utf-8">


<p>After training a model, the xFusionCorp Industries ML team requires DVC to surface model metrics through <code>dvc metrics show</code>. Although the fraud-detection pipeline successfully trains a model and generates a <code>metrics.json</code> file, DVC currently does not recognize this file as a metric. Ensure that the <code>metrics.json</code> file is properly configured to be recognized by DVC.</p>

</div><br><div class="markdown-body text-sm mb-8">


<meta charset="utf-8">


<p>A project exists at <code>/root/code/fraud-detection/</code> with a three-stage DVC pipeline (<code>process_data</code>, <code>split_data</code>, <code>train</code>). The <code>train</code> stage runs <code>src/models/train.py</code>, which writes the model to <code>models/model.pkl</code> and metrics to <code>metrics.json</code>. Do not modify the Python files.</p>
<p>Acceptance criteria:</p>
<ul>
<li>The <code>train</code> stage in <code>dvc.yaml</code> declares <code>metrics.json</code> as a DVC metric output rather than a regular file output, with <code>cache: false</code> so the JSON lives in Git for diff history rather than in the DVC cache.</li>
<li>The pipeline has been reproduced so the metric registration takes effect, and <code>dvc metrics show</code> reports the <code>accuracy</code> and <code>f1_score</code> values from <code>metrics.json</code>.</li>
</ul>
<blockquote>
  <p>Tip: once the metric is registered, <code>dvc metrics diff</code> compares its values across Git commits, which is useful when iterating on the model.</p>
</blockquote>
</div>

---

## 🚀 Complete Solution

1. **Review Current Pipeline Configuration:**
* Navigated to `/root/code/fraud-detection/`.
* Inspected `dvc.yaml` and noted that `metrics.json` was listed under the `outs:` (standard outputs) section of the `train` stage.


2. **Modify `dvc.yaml` for Metrics Tracking:**
* Edited the `dvc.yaml` file to explicitly declare `metrics.json` as a metric.
* Removed `metrics.json` from the `outs:` block.
* Added a `metrics:` block with the `cache: false` flag.
* *Modified `train` block in `dvc.yaml`:*
```yaml
  train:
    cmd: python3 src/models/train.py
    deps:
      - data/processed/train.csv
      - src/models/train.py
    outs:
      - models/model.pkl
    metrics:
      - metrics.json:
          cache: false

```




3. **Reproduce the Pipeline:**
* Executed `dvc repro` to apply the configuration changes. DVC skipped the untouched data processing stages and re-ran the `train` stage to properly register the metric output.


4. **Verify Metrics Visibility:**
* Ran `dvc metrics show` to confirm DVC successfully parsed the `metrics.json` file and displayed the `accuracy` and `f1_score` directly in the terminal.



**💡 Key Learnings & Gotchas:**

* **`cache: false`:** This is a crucial concept for DVC metrics. By telling DVC *not* to cache the `metrics.json` file, you are instructing DVC to leave the file alone so Git can track it. Because metrics are usually tiny text/JSON files, storing them in Git allows you to easily see how your model's accuracy changes commit-by-commit over time.
* **`dvc metrics diff`:** Once metrics are tracked in Git, you can run `dvc metrics diff` before committing to instantly compare your current model's performance against the previous commit, ensuring you only merge improvements into your `main` branch.

### 🖥️ Proof of Execution

Below is the terminal trace demonstrating the modification of the pipeline, the successful reproduction, and the verification of the tracked metrics.

```bash
root@controlplane fraud-detection on  main ➜  cat dvc.yaml
# [...] (Initial configuration omitted)
  train:
    cmd: python3 src/models/train.py
    deps:
      - data/processed/train.csv
      - src/models/train.py
    outs:
      - models/model.pkl
      - metrics.json

root@controlplane fraud-detection on  main ➜  nano dvc.yaml # Moved metrics.json to metrics: block

root@controlplane fraud-detection on  main ➜  dvc repro
Stage 'process_data' didn't change, skipping
Stage 'split_data' didn't change, skipping
Running stage 'train':
> python3 src/models/train.py
Updating lock file 'dvc.lock'

To track the changes with git, run:
        git add dvc.yaml dvc.lock metrics.json

root@controlplane fraud-detection on  main ➜  dvc metrics show
Path          accuracy    f1_score
metrics.json  0.8953      0.8812

```