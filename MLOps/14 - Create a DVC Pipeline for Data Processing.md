# 14 - Create a DVC Pipeline for Data Processing

## 📋 Task Overview

<div class="markdown-body text-base">


<meta charset="utf-8">


<p>The xFusionCorp Industries ML team utilizes DVC pipelines to ensure the reproducibility of data processing. The fraud-detection project has the processing scripts and raw data in place but does not yet define a pipeline. Define a two-stage DVC pipeline so the data processing runs reproducibly from start to finish with <code>dvc repro</code>.</p>

</div><br><div class="markdown-body text-sm mb-8">


<meta charset="utf-8">


<p>A project exists at <code>/root/code/fraud-detection/</code> with DVC initialised. The scripts are at <code>src/data/process_data.py</code> and <code>src/data/split_data.py</code>, and the raw input is at <code>data/raw/transactions.csv</code>. Do not modify the Python files or the input data.</p>
<p>Acceptance criteria:</p>
<ul>
<li>A <code>dvc.yaml</code> defines two stages:<ul>
<li><code>process_data</code> – runs <code>python3 src/data/process_data.py</code>; depends on <code>data/raw/transactions.csv</code> and <code>src/data/process_data.py</code>; produces <code>data/processed/clean_transactions.csv</code>.</li>
<li><code>split_data</code> – runs <code>python3 src/data/split_data.py</code>; depends on <code>data/processed/clean_transactions.csv</code> (the upstream stage's output, so DVC chains the stages) and <code>src/data/split_data.py</code>; produces <code>data/processed/train.csv</code> and <code>data/processed/test.csv</code>.</li></ul></li>
<li>The pipeline has been reproduced so both stages execute in order and <code>dvc.lock</code> is written, and <code>dvc status</code> reports no stale stages.</li>
</ul>
<blockquote>
  <p>Use <code>python3</code> (not <code>python</code>) in the stage commands. Once the pipeline is valid, <code>dvc dag</code> prints the dependency graph showing how the two stages chain together.</p>
</blockquote>

---

## 🚀 Complete Solution

1. **Navigate to the Project Directory:**
Changed into the initialized DVC repository where the raw data (`transactions.csv`) and processing scripts (`process_data.py`, `split_data.py`) were located.
```bash
cd /root/code/fraud-detection/

```


2. **Define the `process_data` Stage:**
Created the first pipeline stage using `dvc stage add` (which automatically generates the `dvc.yaml` file). This stage executes the processing script, explicitly mapping the raw data and script as dependencies (`-d`) and the clean dataset as the output (`-o`).
```bash
dvc stage add -n process_data \
  -d data/raw/transactions.csv \
  -d src/data/process_data.py \
  -o data/processed/clean_transactions.csv \
  python3 src/data/process_data.py

```


3. **Define the `split_data` Stage:**
Created the second pipeline stage. By setting the dependency of this stage to the output of the first stage (`clean_transactions.csv`), DVC automatically understands the dependency chain.
```bash
dvc stage add -n split_data \
  -d data/processed/clean_transactions.csv \
  -d src/data/split_data.py \
  -o data/processed/train.csv \
  -o data/processed/test.csv \
  python3 src/data/split_data.py

```


*(Note: Alternatively, `dvc.yaml` can be authored manually using standard YAML syntax to define these identical stages, dependencies, and outputs).*
4. **Reproduce the Pipeline:**
Executed the pipeline to ensure the stages run in the correct order, generate the processed CSV files, and write the state hashes to `dvc.lock`.
```bash
dvc repro

```


5. **Verification & DAG Visualization:**
Confirmed that the pipeline successfully executed and that no stages were left stale. Finally, printed the Directed Acyclic Graph (DAG) to visualize the pipeline's execution flow.
```bash
dvc status
dvc dag

```



**💡 Key Learnings & Gotchas:**

* **Automatic Chaining:** DVC infers the order of execution (the DAG) automatically based on the overlapping outputs (`-o`) and dependencies (`-d`) between stages. No explicit sequencing parameters are required.
* **Tracking Scripts as Dependencies:** Adding the Python scripts (`src/data/*.py`) as dependencies ensures that if the code logic changes, DVC knows the pipeline is stale and needs to be re-run, even if the raw input data remains exactly the same.