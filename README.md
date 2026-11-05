# Spark Hands-On

A collection of small, local, hands-on projects that explain Spark and Databricks concepts by running them. Each folder takes one concept, builds a tiny version of it you can run on a laptop with no cluster, and prints numbers you can check against an answer.

The projects are the practice ground for a bigger build, the Urban Cart Databricks Lakehouse. Each concept here is a piece of what that pipeline does in production.

## Navigation

| # | Topic | Folder | What you will see |
|---|---|---|---|
| 1 | Spark joins | [`spark-joins/`](spark_joins_hands_on/workshop.ipynb) | A join actually executing: broadcast vs shuffle (sort-merge), which join types broadcast can and can't support, why unmatched rows behave the way they do, and what skew does to a sort-merge join. |
| 2 | CDC from scratch | [`cdc-from-scratch/`](cdc-from-scratch/cdc_from_scratch.ipynb) | What Lakeflow's `create_auto_cdc_flow` does, rebuilt by hand in PySpark: out-of-order events, stale updates after a delete, duplicates, key-only deletes, and the tombstone window, checked against a known answer of 8,170 live customers. |

Start with the joins folder if Spark is new to you. The CDC folder assumes you are comfortable with window functions and joins.

## Setup (shared by every folder)

Everything here runs inside **WSL (Ubuntu)**, not native Windows. PySpark on native Windows needs extra `winutils`/`HADOOP_HOME` setup that buys nothing for these exercises, and a real Spark cluster runs on Linux anyway, so practicing on Ubuntu now matches what you'd see in production later.

1. **Open a WSL Ubuntu terminal** (install WSL2 + Ubuntu first if you haven't: `wsl --install -d Ubuntu` from Windows PowerShell, then restart).

2. **Install Java.** PySpark is JVM-based under the hood and needs a JRE:
   ```bash
   sudo apt update
   sudo apt install openjdk-17-jre-headless -y
   java -version   # confirm it printed a version
   ```
   If `JAVA_HOME` isn't already set, find it and export it:
   ```bash
   readlink -f $(which java)   # e.g. /usr/lib/jvm/java-17-openjdk-amd64/bin/java
   echo 'export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64' >> ~/.bashrc
   echo 'export PATH=$JAVA_HOME/bin:$PATH' >> ~/.bashrc
   source ~/.bashrc
   ```

3. **Create and activate a virtual environment** at the root of this repo, on the Linux filesystem (not `/mnt/c/...`, which is slower and has permission quirks):
   ```bash
   cd ~/projects/Spark_hands_on
   python3 -m venv .venv
   source .venv/bin/activate
   ```

4. **Install the Python packages** inside the venv. If you hit an `externally-managed-environment` error, plain `pip` isn't resolving to the venv, so use the venv's Python module directly:
   ```bash
   python3 -m pip install pyspark pandas ipykernel jupyter
   python3 -m pip show pyspark   # confirm it installed
   ```

5. **Connect VS Code to WSL**, so it runs as a Linux process instead of trying to reach across from Windows (this avoids a `spawn EFTYPE` kernel error):
   - Install the **WSL** extension (`ms-vscode-remote.remote-wsl`) in VS Code.
   - From your WSL terminal, inside the repo folder, run:
     ```bash
     code .
     ```
   - Confirm the bottom-left corner of the VS Code window now says **WSL: Ubuntu**.
   - Install the Python and Jupyter extensions *inside* this WSL-connected window if prompted (extensions are scoped per window type).

6. **Open a notebook and select the venv kernel.** Click *Select Kernel* at the top right of the notebook, choose *Python Environments*, then pick `.venv`.

## Running a project

Each folder is self-contained: its notebook, its data and its answer key sit side by side. Open the notebook from inside its folder and run it top to bottom. The notebooks use relative paths such as `customers_cdc/*.json`, so the folder layout must stay as it is.

If Spark fails to start with `JAVA_GATEWAY_EXITED`, Java isn't installed or `JAVA_HOME` isn't set. Go back to step 2.

## Repo layout

```
Spark_hands_on/
├── README.md
├── .venv/                      # created in setup, not committed
├── spark-joins/
│   └── ...                     # joins notebook and its README
└── cdc-from-scratch/
    ├── cdc_from_scratch.ipynb  # the notebook
    ├── customers_cdc/          # 4 JSON files: a seed plus 3 weekly batches
    └── cdc_ground_truth.json   # the answer key the notebook checks against
```

Add a `.venv/` line to `.gitignore` so the environment isn't committed.
.