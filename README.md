Fraud Detection — Streamlit App + scikit-learn Pipeline
An end-to-end demo that predicts whether a transaction is fraudulent (1) or not (0) using a scikit-learn pipeline, with a simple Streamlit UI.

This project follows the tutorial flow: EDA → feature engineering → model training/evaluation → export pipeline → Streamlit app.

Dataset
Source (Kaggle): Fraud Detection Dataset
https://www.kaggle.com/datasets/amanalisiddiqui/fraud-detection-dataset?resource=download

~6.36M rows, 11+ columns

Typical columns:
step, type, amount, nameOrig, oldbalanceOrg, newbalanceOrg,
nameDest, oldbalanceDest, newbalanceDest, isFraud (target), isFlaggedFraud

Severe class imbalance: fraud rate ≈ 0.13% → accuracy alone is not meaningful.

What’s in this repo
analysismodel.ipynb – EDA, feature prep, model training/eval

fraud_detection.py – Streamlit app (loads model and makes predictions)

fraud_detection_pipeline.pkl – exported trained pipeline (joblib)

requirements.txt, .gitignore

README.md (this file)

The model artifact was saved with scikit-learn==1.5.1 and the app pins that version for compatibility.

EDA (highlights)
From the notebook:

Transaction types: CASH_OUT, PAYMENT, TRANSFER, DEBIT, CASH_IN — volume led by CASH_OUT.

Fraud by type: Almost all fraud appears in TRANSFER and CASH_OUT; others are ~0.

Amounts: Heavy-tailed distribution; visualized with log-scaled histogram.

Boxplot (amount vs. isFraud, under 50k): higher amounts correlate with higher fraud rate.

Balance deltas: Created diagnostic features balanceDiff_Org and balanceDiff_Dest to reveal anomalies.

Time (step): Fraud counts vs. time show no clear trend → dropped before modeling.

Correlations: Strong relationships among destination balances; moderate correlation with amount.

Features & Target used for modeling
Target: isFraud (0/1)

Features kept:
Categorical — type
Numeric — amount, oldbalanceOrg, newbalanceOrig, oldbalanceDest, newbalanceDest

Dropped before modeling: nameOrig, nameDest, isFlaggedFraud, step

Model & Training
Train/test split: 70/30 with stratify=y

Preprocessing: ColumnTransformer

OneHotEncoder on type (drop first category)

StandardScaler on numeric features

Classifier: LogisticRegression(class_weight="balanced", max_iter=1000+)

class_weight="balanced" helps with the ~0.13% positive class

Evaluation: classification report + confusion matrix on the test set
Test set: Accuracy 0.95; Fraud (1) — Precision 0.02, Recall 0.94, F1 0.04; Non-fraud (0) — Precision 1.00, Recall 0.95, F1 0.97.

Export: joblib.dump(pipeline, "fraud_detection_pipeline.pkl")

Streamlit app (what the current file does)
File: fraud_detection.py
Loads: fraud_detection_pipeline.pkl with joblib
Inputs (UI):

Transaction Type (select box) — ["PAYMENT", "TRANSFER", "CASH_OUT", "DEPOSIT"]

Amount (float, default 1000.0)

Old Balance (Sender) (float, default 10000.0)

New Balance (Sender) (float, default 9000.0)

Old Balance (Receiver) (float, default 0.0)

New Balance (Receiver) (float, default 0.0)

Predict button builds a one-row DataFrame with columns:
type, amount, oldbalanceOrg, newbalanceOrig, oldbalanceDest, newbalanceDest,
feeds it to model.predict(), and displays the class (0/1) and a friendly message.

Heads-up: your app currently lists "DEPOSIT" as a transaction option. Many Kaggle versions use "CASH_IN". Make sure the label matches whatever the model was trained on. If the encoder wasn’t set with handle_unknown="ignore", an unseen label will error. (Change "DEPOSIT" → "CASH_IN" if needed.)

Run locally
powershell
Copy
Edit
# Windows (PowerShell)
py -3.12 -m venv .venv
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass -Force
.\.venv\Scripts\Activate.ps1

python -m pip install --upgrade pip
pip install -r requirements.txt

streamlit run fraud_detection.py
# App: http://localhost:8501
