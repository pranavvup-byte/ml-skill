# CKD Mini Hackathon 1 — Project Brief

## Problem Statement
Chronic Kidney Disease (CKD) can progress without obvious symptoms. The project investigates whether routinely available clinical variables can support early CKD screening with machine learning.

## Objectives
1. Audit three candidate CKD datasets and select the most defensible project dataset.
2. Perform systematic EDA and identify clinically relevant patterns, missingness, imbalance, and leakage risks.
3. Build and compare XGBoost and LightGBM classifiers.
4. Evaluate accuracy, precision, recall, F1, ROC-AUC, confusion matrices, and training time.
5. Provide feature-importance interpretation and a small demonstration prototype.

## Dataset decision
Selected: `CKD_NHANES_2021_2023(1).csv` — 11,933 rows, 29 columns.

### Why selected
- Largest of the three supplied datasets.
- Rich clinical and demographic variables.
- Stronger basis for EDA than the 400-row classic dataset.
- More clinically structured than the 4,800-row no-missing dataset.

### Comparison
| Dataset | Rows | Columns | Missing cells | Target | Decision |
|---|---:|---:|---:|---|---|
| CKD_NHANES_2021_2023 | 11,933 | 29 | 114,393 | ckd_present | **Primary** |
| Testing_CKD_dataset | 4,800 | 36 | 0 | Target | Secondary/sanity-check |
| kidney_disease | 400 | 26 | 1,009 | classification | Baseline/reference |

## Leakage policy
`participant_id` is removed as an identifier. `ckd_stage` is removed because it is a downstream label. `egfr` is removed from the primary model because the supplied target is almost deterministic across eGFR bins; including it would risk learning the target definition directly.

## Preprocessing
- Treat implausible age value (<1 year) as missing.
- Median imputation for numeric variables.
- Most-frequent imputation + one-hot encoding for categorical variables.
- Stratified 80/20 train-test split with random_state=42.
- Tree models are not scale-sensitive, so normalization is not required for XGBoost/LightGBM; this is preferable to adding unnecessary transformations.

## Experimental setup
XGBoost and LightGBM were tuned previously with 3-fold cross-validation on the training portion; the primary report preserves the best configurations and evaluates on the held-out test set.

## Prototype
A Streamlit app loads the trained pipelines, accepts clinical inputs, displays predicted CKD probability, and allows model comparison. It is explicitly labeled as an educational decision-support prototype, not a medical diagnostic tool.

## Research papers
1. Chen & Guestrin (2016), *XGBoost: A Scalable Tree Boosting System*, KDD 2016. https://arxiv.org/abs/1603.02754
2. Ke et al. (2017), *LightGBM: A Highly Efficient Gradient Boosting Decision Tree*, NeurIPS 2017. https://papers.nips.cc/paper/2017/hash/6449f44a102fde848669bdd9eb6b76fa-Abstract.html
3. Capodici et al. (2023), *Predict, diagnose, and treat chronic kidney disease with machine learning: a systematic literature review*. https://pubmed.ncbi.nlm.nih.gov/36786976/
4. *Artificial Intelligence to Predict Chronic Kidney Disease Progression to Kidney Failure: A Narrative Review* (2025). https://pubmed.ncbi.nlm.nih.gov/39763163/

## Main result
On the supplied dataset and leakage-controlled feature set, both models show very high test performance. These numbers should be presented as dataset-specific experimental results, not as clinical validation.
