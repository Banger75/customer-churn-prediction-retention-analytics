# Customer Churn Prediction & Retention Analytics

Portfolio-ready **notebook workflow** for the public IBM Telco Customer Churn dataset.

## Scope
- Dataset audit, blank `TotalCharges` treatment and exploratory analysis
- Contract vs churn Chi-square test, Cramér’s V, risk difference, relative risk and odds ratio
- Leakage-safe train/validation/test splits; logistic regression and random forest pipelines
- PR-AUC, ROC-AUC, Brier score, confusion matrix, F1 and validation-selected threshold
- Hypothetical retention targeting constrained by a budget (causal effect assumed, **not estimated**)

## Run locally (Windows PowerShell)
```powershell
uv venv .venv --python 3.12
.\.venv\Scripts\Activate.ps1
uv pip install -r requirements.txt
mkdir data
# Download the IBM CSV into data\Telco-Customer-Churn.csv
jupyter notebook notebooks/01_customer_churn_end_to_end.ipynb
```

Original dataset: https://github.com/IBM/telco-customer-churn-on-icp4d/blob/master/data/Telco-Customer-Churn.csv

Expected shape: **7,043 rows × 21 columns**. The notebook expects this file to be supplied under `data/`; do not commit confidential datasets.

## Interpretation caveats
Results are observational associations and model predictions, not causal claims. The 20% treatment effectiveness and all campaign financial inputs are **hypothetical assumptions**. For actual campaign recommendations, measure treatment uplift with an experiment. The notebook must be run on the local dataset to obtain scores; no performance results are preclaimed here.
