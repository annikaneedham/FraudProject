# Fraud Detection Streamlit App

A compact demo that predicts the probability a transaction is fraudulent using a scikit‑learn pipeline and a Streamlit UI.

## What this demonstrates
- End‑to‑end: data prep ➜ model ➜ serialized artifact ➜ interactive app
- Reproducible environment with pinned dependencies
- Clear documentation for recruiters

## Tech
- Python 3.12
- scikit‑learn **1.5.1** (matches the saved pipeline)
- Streamlit, pandas, numpy, joblib

## Dataset
Replace with your actual source (e.g., Kaggle link) and column names. Target column: `is_fraud` (0/1).

## Model
- Pipeline: `ColumnTransformer` (OneHotEncoder + StandardScaler) ➜ classifier (update to your actual choice)
- Saved as: `fraud_detection_pipeline.pkl` (pickle via joblib)
- Note: Pickled sklearn objects are version‑sensitive; we pin **scikit‑learn==1.5.1**.

## Quick Start (Windows / PowerShell)
```powershell
py -3.12 -m venv .venv
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass -Force
.\.venv\Scripts\Activate.ps1

python -m pip install --upgrade pip
pip install -r requirements.txt

streamlit run fraud_detection.py
```
App: http://localhost:8501

## One‑click Deploy (Streamlit Community Cloud)
1) Push to GitHub (public).
2) On share.streamlit.io ➜ New app ➜ select repo ➜ file: `fraud_detection.py`.
3) Keep `requirements.txt` (pins sklearn 1.5.1). Optional: include `runtime.txt` with `python-3.12.7`.

## Project Structure
```
.
├── fraud_detection.py
├── fraud_detection_pipeline.pkl   # include if <100MB; remove *.pkl from .gitignore first
├── requirements.txt
├── runtime.txt                    # optional (for Streamlit Cloud)
├── README.md
└── .gitignore
```

## Optional: Version‑safe model with skops
```python
from skops.io import dump, load
dump(model, "fraud_detection_pipeline.skops")
# in app:
model = load("fraud_detection_pipeline.skops")
```
Then add `skops` to requirements and omit the `.pkl`.
