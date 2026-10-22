# Evaluation evidence and next steps

## Reference implementation
Use notebook 02. Notebook 01 is an earlier overlapping workflow: it selects by validation ROC-AUC and uses a different campaign budget. Both currently record the same selected model and held-out classification results; they are not independent experiments.

## What is implemented
- Stratified 60/20/20 split with seed 42.
- Training-fitted preprocessing in separate model pipelines in notebook 02.
- Validation AP selection: Logistic Regression 0.6424; Random Forest 0.6344.
- Validation F1 threshold selection over a grid: 0.35.
- Held-out test ROC-AUC 0.8430, AP 0.6345, Brier 0.1377.
- Test precision 0.5473, recall 0.7112 and F1 0.6186.
- Validation-based campaign simulation with hypothetical economics.

These values are recorded outputs, not an independent reproduction during this documentation review.

## Why these metrics
AP summarizes positive-class ranking and should be interpreted alongside prevalence (26.54% on test). It is not trapezoidal PR area. ROC-AUC summarizes positive/negative ranking across thresholds. Precision describes outreach yield; recall describes coverage of observed churners. F1 balances these without representing financial utility.

Brier measures squared probability error and reflects more than calibration. A training-prevalence constant predictor provides an approximate Brier reference of 0.195 and ROC-AUC 0.5; majority-class accuracy is about 73.5%. Add these as explicitly computed baselines rather than relying on accuracy alone.

## Evidence gaps, in priority order

| Priority | Gap | Concrete next evidence |
| --- | --- | --- |
| 1 | Exact reproduction unverified | Clean-kernel execution; package versions, lockfile, CSV hash and machine-readable metrics |
| 2 | Full-data target-aware EDA precedes splitting | Confine future target-aware exploration to development data; evaluate new decisions on independent data |
| 3 | Calibration not demonstrated | Reliability diagram with bin counts, log loss and Brier baseline on validation data |
| 4 | Single validation split | Training-only stratified CV for model stability; keep preprocessing inside folds |
| 5 | Small validation AP difference | Report variability rather than claiming Logistic Regression decisively outperforms Random Forest |
| 6 | Capacity-specific usefulness missing | Validation precision@k, recall@k and lift@k for an explicit contact capacity |
| 7 | No uncertainty or subgroup analysis | Prespecified bootstrap intervals for frozen predictions; segment sample sizes and performance |
| 8 | No measured retention effect | Randomized offer/control experiment; incremental net contribution and retention over a defined horizon |

If probability calibration is added, fit the calibrator using training-only out-of-fold predictions or a distinct calibration split. Avoid fitting and assessing it on the same labels. Select the method before final independent evaluation. The current test has already been viewed, so repeated improvements on it should not be described as fresh confirmatory evidence.

## Business policy
The canonical scenario assumes equal ₹500 contact costs, ₹5,000 incremental value per retained would-be churner and 20% prevention effectiveness. Expected net benefit is p(churn) × 0.20 × 5000 − 500. Select positive-net-benefit customers in descending order up to 100 offers under a ₹50,000 budget.

The effectiveness assumption is conditional on would-be churners, not a measured 20 percentage-point uplift for every customer. High churn risk does not imply high responsiveness. Test effectiveness, contribution value and adverse effects experimentally; use sensitivity analysis before treating the scenario as a recommendation.

## Scope boundaries
No calibrated model, causal uplift model, production deployment, time-based validation, automated CI or realized business return is demonstrated. Standardized numeric coefficients describe one-standard-deviation changes; full one-hot encoding does not define a single omitted reference category. Coefficients are predictive associations under regularization, not causal effects.
