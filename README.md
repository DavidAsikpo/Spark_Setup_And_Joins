# Spark Joins Hands-On

A local, hands-on walkthrough of how Spark actually executes a join — broadcast
vs. shuffle (sort-merge), which join types broadcast can and can't support,
why unmatched rows behave the way they do, and what skew does to a sort-merge
join. All of it runs on a laptop, no cluster required.

## Setup

Everything here runs inside **WSL (Ubuntu)**, not native Windows — PySpark on
native Windows needs extra `winutils`/`HADOOP_HOME` setup that buys nothing for
this exercise, and a real Spark cluster runs on Linux anyway, so practicing on
Ubuntu now matches what you'd see in production later.

1. **Open a WSL Ubuntu terminal** (install WSL2 + Ubuntu first if you haven't:
   `wsl --install -d Ubuntu` from Windows PowerShell, then restart).

2. **Install Java** — PySpark is JVM-based under the hood and needs a JRE:
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

3. **Create and activate a virtual environment**, inside the project folder,
   on the Linux filesystem (not `/mnt/c/...` — that mount is slower and has
   permission quirks):
   ```bash
   cd ~/projects/Spark_hands_on
   python3 -m venv .venv
   source .venv/bin/activate
   ```

4. **Install PySpark** inside the venv. If you hit an
   `externally-managed-environment` error, that means plain `pip` isn't
   resolving to the venv — use the venv's Python module directly instead:
   ```bash
   python3 -m pip install pyspark
   python3 -m pip show pyspark   # confirm it installed
   ```

5. **Connect VS Code to WSL**, so it runs as a Linux process instead of trying
   to reach across from Windows (this avoids a `spawn EFTYPE` kernel error):
   - Install the **WSL** extension (`ms-vscode-remote.remote-wsl`) in VS Code.
   - From your WSL terminal, inside the project folder, run:
     ```bash
     code .
     ```
   - Confirm the bottom-left corner of the VS Code window now says
     **WSL: Ubuntu**.
   - Install the Python and Jupyter extensions *inside* this WSL-connected
     window if prompted (extensions are scoped per window type).

6. **Open the notebook and select the venv kernel** — see below.

## Where the actual lesson is

Everything else — cluster sizing, broadcast vs. shuffle joins, which join
types broadcast supports, the outer-join gotcha, the datasets, and production
best practices — lives in the notebook, not here:

**[`scripts/workshop.ipynb`](scripts/workshop.ipynb)**

Work through it top to bottom in order; later cells depend on the
`SparkSession` and datasets created earlier in the notebook.