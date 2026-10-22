# Data understanding

## Source and unit of analysis
The project uses the IBM Telco Customer Churn CSV linked in the README. One row represents a customer snapshot. The expected file has 7,043 rows and 21 columns, with 7,043 unique customer identifiers. Churn is labeled Yes/No; Yes is encoded as 1.

The CSV alone does not establish a scoring timestamp, a future prediction horizon, or when each feature became available. Treat this as retrospective classification until those definitions are documented. Confirm source terms before redistributing the data.

## Feature dictionary

| Group | Fields | Treatment |
| --- | --- | --- |
| Identifier | customerID | Audit uniqueness; exclude from predictors |
| Target | Churn | Yes = 1; No = 0; never a predictor |
| Numeric | tenure, MonthlyCharges, TotalCharges | Training-fitted median imputation and standardization |
| Demographic categories | gender, SeniorCitizen, Partner, Dependents | One-hot encode; SeniorCitizen is categorical despite numeric coding |
| Services | PhoneService, MultipleLines, InternetService, OnlineSecurity, OnlineBackup, DeviceProtection, TechSupport, StreamingTV, StreamingMovies | One-hot encode; preserve service-absence categories |
| Billing categories | Contract, PaperlessBilling, PaymentMethod | One-hot encode |
| Derived exploration | TenureGroup | Used for EDA; excluded from modeling |

Tenure is expressed in months. Do not assume the source charges are Indian rupees: the ₹ amounts in the campaign are independent hypothetical financial inputs.

## Recorded quality findings
- 5,174 No and 1,869 Yes outcomes; churn prevalence 26.54%.
- No duplicate customerIDs recorded.
- Raw null checks report zero, but 11 TotalCharges entries are blank text.
- Those 11 entries have tenure zero. The notebooks convert them to numeric zero under a no-completed-billing-period assumption, preserving all rows.
- Invalid charges with nonzero tenure raise an error. Production use requires billing-domain confirmation; coercion should not silently conceal other malformed values.

## Interpretation
Observed rates differ across tenure and contract groups. The contract Chi-square statistic is 1,184.597 (2 degrees of freedom), with Cramér’s V 0.410. Month-to-month versus two-year observed churn is 42.71% versus 2.83%, a 39.88 percentage-point difference. Confounding and selection prevent interpreting this as the benefit of changing contracts.

## Reproducibility gaps
Archive a source commit or immutable download reference, acquisition date and SHA-256 checksum after retrieving the CSV. Add complete schema/range checks, expected-category checks and source definitions. Establish feature availability before any future prediction exercise.
