# MemForensic
####
This is meant to build out an agentic digital forensic analysis for memory leveraging Volatility 3. The setup is demonstrated as:
The analysis tier of the memory forensics app. Give it a memory image in Cloud Storage and it verifies the image's hash, runs a Volatility 3 plugin profile in parallel, archives the raw output, and loads every row into BigQuery, where typed views and starter detections sit on top.

```
GCS evidence ──► download + SHA-256 check ──► OS detection ──► parallel `vol` processes
                                                                   │
              BigQuery plugin_rows / plugin_runs ◄── flatten ◄─────┤
              GCS raw JSON + stderr + manifest.json ◄──────────────┘
```

#### 
## Layout

| Path | What it is |
|---|---|
| `worker/main.py` | Orchestrator: download, verify, detect, run, load, manifest |
| `worker/runner.py` | Runs each plugin as its own `vol` subprocess with a timeout |
| `worker/flatten.py` | Flattens Volatility's nested `__children` JSON into rows |
| `worker/bq.py` | Creates tables, loads rows with load jobs |
| `worker/gcs.py` | Evidence download + hash check, symbol cache sync, uploads |
| `worker/bootstrap.py` | Creates tables and views (run once) |
| `plugins.yaml` | Plugin profiles for Windows, Linux, macOS |
| `sql/views.sql` | Typed views and detection views |
| `deploy/` | Cloud Batch setup, job template, submit script |
| `tests/` | Unit tests, including a check that every profile entry exists in the installed Volatility |