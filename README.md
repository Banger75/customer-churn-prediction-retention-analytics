# Customer Churn Prediction & Retention Analytics

Identify customers at risk of churn and explore how a retention team could prioritize limited outreach. This portfolio project combines data auditing, statistical association, leakage-aware model pipelines, held-out evaluation, and a hypothetical campaign policy.

**Start here:** [End-to-end notebook](notebooks/02_customer_churn_end_to_end.ipynb) · [Evaluation and limitations](docs/evaluation.md) · [Data guide](docs/data.md)

## Results already recorded in the notebook

These are the committed notebook outputs, inspected during this review; they have not been independently rerun in a locked environment.

| Evidence | Recorded result |
| --- | --- |
| Dataset | 7,043 customers; 1,869 churners (26.54%) |
| Split | 4,225 training / 1,409 validation / 1,409 test; stratified, seed 42 |
| Selected model | Logistic Regression; validation average precision 0.6424 vs Random Forest 0.6344 |
| Test ROC-AUC | 0.8430 |
| Test average precision (AP) | 0.6345; test prevalence reference 0.2654 |
| Test precision / recall / F1 | 0.5473 / 0.7112 / 0.6186 |
| Classification threshold | 0.35, selected by validation F1 |
| Test Brier score | 0.1377; a probability-error measure, not proof of calibration |

AP is computed with `average_precision_score`, not trapezoidal area under the PR curve. At the selected threshold, about 71% of observed churners are identified, while about 55% of flagged customers actually churn. This is a tradeoff for outreach, not evidence that an offer will prevent churn.

## Project map

```text
notebooks/
  01_data_understanding.ipynb       Earlier exploration and baseline workflow
  02_customer_churn_end_to_end.ipynb Canonical portfolio workflow
docs/
  data.md                          Source, schema, cleaning and prediction limits
  evaluation.md                    Selection rules, evidence gaps and next checks
requirements.txt                   Compatible dependency ranges (not a lockfile)
```

Notebook 01 also includes modeling and a ₹15,000 campaign scenario. It is retained as earlier exploratory work, not an independent replication. Notebook 02 is the reference for this README and uses a ₹50,000 scenario. Do not mix outputs between the two.

## Workflow and technical decisions

1. Audit identifiers, target labels, types and blank charges. Convert 11 blank `TotalCharges` values to zero only when tenure is zero, under an explicit billing assumption.
2. Explore tenure and contracts; report Chi-square, Cramér’s V, risk difference, relative risk and odds ratio as associations.
3. Exclude `customerID`, the target and the EDA-derived `TenureGroup`. Split before fitting imputation, scaling and one-hot encoding.
4. Fit Logistic Regression and Random Forest pipelines on training data; select by validation AP.
5. Choose an illustrative F1 threshold on validation data; report final test ranking, classification and probability-error metrics.
6. Simulate a budget-limited policy using validation probabilities and explicitly hypothetical costs and treatment effectiveness.

**Validation limit:** target-aware EDA currently precedes the split and uses the full dataset. Fitted preprocessing is confined to training data, but the overall exploratory process is not a completely untouched holdout protocol. Future feature decisions should use development data, followed by a new independent or prospective evaluation.

## Reproduce locally

Use Python 3.12. From a cloned repository root, in Windows PowerShell:

```powershell
git clone https://github.com/Banger75/customer-churn-prediction-retention-analytics.git
cd customer-churn-prediction-retention-analytics
uv venv .venv --python 3.12
.\.venv\Scripts\Activate.ps1
uv pip install -r requirements.txt
New-Item -ItemType Directory -Force data | Out-Null
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/IBM/telco-customer-churn-on-icp4d/master/data/Telco-Customer-Churn.csv" -OutFile "data/Telco-Customer-Churn.csv"
python -m ipykernel install --user --name churn-portfolio --display-name "Churn portfolio"
jupyter notebook notebooks/02_customer_churn_end_to_end.ipynb
```

Select **Churn portfolio**, restart the kernel and run all cells in order. No trained model is required. The data is excluded by `.gitignore`. Source: [IBM repository CSV](https://github.com/IBM/telco-customer-churn-on-icp4d/blob/master/data/Telco-Customer-Churn.csv).

Reproducibility is partial: dependency ranges are supplied, but there is no tested lockfile, data checksum or automated execution check. Notebook metadata records Python 3.12.13; a recorded pandas dtype representation also warrants checking the actual package versions. Capture a verified environment and source-data hash after a clean rerun rather than claiming exact reproduction now.

## Business interpretation and proposed actions

- Investigate early-tenure onboarding and month-to-month customer experience. Observed churn is 47.44% for tenure 0–12 months and 42.71% for month-to-month contracts; these overlaps do not establish causal effects.
- Test outreach with randomized treatment/control groups. Measure incremental retention and net contribution over a defined follow-up period, including offer and contact costs.
- Match outreach capacity to validation precision/recall and measured economics. The F1 threshold is a classification illustration, not a profit-maximizing campaign rule.
- Check calibration before using probabilities in financial calculations. Add segment performance, uncertainty and drift checks before deployment.

The canonical campaign assumes ₹500 per contact, ₹5,000 retained-customer value and 20% effectiveness among would-be churners. A ₹50,000 budget permits 100 offers; the recorded modeled net benefit is ₹24,121. **This is scenario arithmetic, not realized revenue or measured uplift.** Under these assumptions the break-even churn probability is 50%, distinct from the 35% F1 threshold.

## Limits and next evidence to add

This public snapshot supports a retrospective demonstration, not a validated future-month forecast. Prediction timestamps, outcome windows and feature availability must be established for deployment. Calibration curves, log loss, confidence intervals, repeated validation, capacity-based lift and subgroup checks are not yet implemented. See [the evaluation plan](docs/evaluation.md) for priorities.
