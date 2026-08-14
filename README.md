# Credit Default Prediction: Tree-Based Model Validation

An educational quantitative-risk project that predicts whether a credit-card account will default in the following month, then evaluates the model as a validation analyst would: against baselines, across model choices, thresholds, calibration, split sensitivity, feature reliance, and a high-risk subgroup.

> This is a portfolio and learning project using historical public data. It is **not** a lending, pricing, or decisioning system, and predictive relationships in this notebook must not be interpreted as causal effects.

## Highlights

- Compared an all-no-default baseline, logistic regression, a small decision tree, a cross-validated pruned tree, random forest, and gradient boosting.
- Demonstrated severe overfitting in an unpruned tree: training ROC-AUC of `1.0000` versus development-holdout ROC-AUC of `0.6052`.
- Selected gradient boosting as the preferred candidate, with development-holdout ROC-AUC of `0.7763`.
- Tested threshold trade-offs, calibration, split stability, permutation importance, and subgroup performance.
- Documented material limitations, including weaker ranking performance for already seriously delinquent accounts.

## Business question

Given account information observed before the next billing cycle, can a model estimate the probability that a credit-card customer defaults in the following month?

The target is a binary variable:

- `1` — default next month
- `0` — no default next month

## Data

The notebook downloads the **Default of Credit Card Clients** dataset through [`ucimlrepo`](https://archive.ics.uci.edu/dataset/350/default), so no manual dataset download is required.

| Item | Detail |
|---|---|
| Observations | 30,000 credit-card accounts |
| Candidate features | 23 |
| Default rate | 22.12% (6,636 defaults) |
| Missing values | 0 |
| Source | Yeh, I. (2009), UCI Machine Learning Repository |
| Licence | CC BY 4.0 |

The source encodes features as `X1` to `X23`; the notebook maps them to documented names such as `LIMIT_BAL`, `PAY_0`, `BILL_AMT1`, and `PAY_AMT1`.

## Methods

1. Audit the data and verify dimensions, target class counts, feature mapping, and missingness.
2. Create a reproducible, stratified 75%/25% training/development-holdout split.
3. Establish a naive all-no-default baseline.
4. Fit logistic regression as a transparent benchmark.
5. Fit a small interpretable decision tree and visualise its rules.
6. Demonstrate overfitting with an unrestricted tree.
7. Select a cost-complexity-pruned tree using five-fold cross-validation.
8. Compare random forest and gradient boosting ensembles.
9. Validate the preferred candidate using threshold analysis, calibration, split sensitivity, permutation importance, and subgroup analysis.

## Results

| Model | Accuracy | Precision | Recall | Development ROC-AUC |
|---|---:|---:|---:|---:|
| All-no-default baseline | 77.88% | 0.00% | 0.00% | 0.5000 |
| Small decision tree | 81.83% | 66.16% | 36.53% | 0.7308 |
| Cross-validated pruned tree | 81.79% | 66.26% | 35.99% | 0.7614 |
| Random forest | 81.71% | 65.79% | 36.05% | 0.7741 |
| Gradient boosting | **81.95%** | **67.04%** | **36.17%** | **0.7763** |

Gradient boosting was the preferred candidate because it had the strongest development-holdout ranking performance. Across five alternative stratified splits, its ROC-AUC averaged `0.7848` with standard deviation `0.0081`.

### Threshold selection

At a default 0.50 threshold, the model prioritises precision and misses many defaults. A 0.30 threshold produced the highest F1 score in this exercise.

| Threshold | Precision | Recall | F1 score | Accounts flagged |
|---|---:|---:|---:|---:|
| 0.20 | 43.80% | 65.34% | 0.5244 | 2,475 |
| **0.30** | **56.01%** | **51.72%** | **0.5378** | **1,532** |
| 0.50 | 67.04% | 36.17% | 0.4699 | 895 |

This is a candidate operating threshold only. A real institution would select a threshold from expected loss, review capacity, regulation, and customer-impact considerations—not F1 alone.

## Key validation findings

- **Calibration:** broadly reasonable at high predicted risk (70.29% predicted vs 69.47% observed), but the lowest-risk bin was overestimated (7.15% vs 4.13%).
- **Feature reliance:** shuffling `PAY_0` reduced ROC-AUC by `0.0836`, making most-recent repayment status the strongest observed predictor. This is predictive reliance, not causation.
- **Subgroup limitation:** ROC-AUC fell from `0.7023` for `PAY_0 < 2` to `0.6040` for `PAY_0 >= 2`. At threshold 0.30, the model flagged every account in the latter group, offering limited prioritisation within an already high-risk segment.

## Repository structure

```text
.
├── credit_default_model_validation.ipynb          # Main analysis notebook
├── credit_default_model_validation_report.docx    # Written validation report
├── README.md                                      # Project overview and results
├── requirements.txt                               # Python dependencies
├── .gitignore                                     # Files excluded from version control
├── data/
│   └── README.md                                  # Data-download strategy and citation
└── data/raw/                                     # Empty; downloaded data is not committed
```

## Getting started

Clone the repository and create a virtual environment.

```bash
git clone <your-repository-url>
cd <your-repository-directory>
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook
```

Open `credit_default_model_validation.ipynb` and run the cells from top to bottom. The notebook retrieves the UCI dataset automatically.

## Limitations and next steps

- The development holdout informed model comparison, so it is not a fully untouched final test set.
- The public dataset is historical and static; no temporal or out-of-time validation was possible.
- The project does not include a cost-based decision policy or production monitoring.
- The analysis is observational and predictive, not causal.

Potential extensions:

1. Reserve a final out-of-time sample and evaluate it only once.
2. Calibrate probabilities within cross-validation using isotonic regression or Platt scaling.
3. Select thresholds from an expected-cost framework.
4. Add model monitoring for performance, calibration, and population drift.
5. Conduct a governance and fairness review appropriate to the intended use.

## Citation

Yeh, I. (2009). *Default of Credit Card Clients* [Dataset]. UCI Machine Learning Repository. [https://doi.org/10.24432/C55S3H](https://doi.org/10.24432/C55S3H)
