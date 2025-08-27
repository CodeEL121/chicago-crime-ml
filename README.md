# Chicago Crime — Arrest Likelihood Prediction (2016)

This project builds classification models to **predict the probability of an arrest** for reported crimes in Chicago.  

- **Dataset:** Chicago Crime (Kaggle) with records from **2012–2016**; for size/reproducibility this repo uses **2016 only**.
- **Outcome:** `Arrest` (binary). We estimate **P(Arrest = 1)**.
- **Best model:** **Logistic Regression** (performed best overall and was stable/interpretable).

---

## Problem
Given incident attributes (offense type, time-of-day, location context, etc.), **predict whether an arrest occurs**.  
The goal is to surface patterns that correlate with arrests and provide a baseline decision-support signal.

---

## Data
- Source: Chicago crime records (Kaggle), 2012–2016 available.
- This repo uses the **2016 subset** (large; raw kept out of git) and also includes a **5k-row sample** for fast demos.
- File locations:
  - `data/raw/Chicago_Crimes_2016.csv` *(ignored by git)*
  - `data/processed/sample_2016_5k.csv` *(safe to commit)*

---

## Methods

### Feature Engineering
- Time features: **hour bins**, **day-of-week bins**, **season** flags.
- Crime grouping: **violent vs. non-violent** aggregation for interpretability.
- Geographic/administrative fields: District, Ward, Community Area (kept or binned).
- Missing data handling with `SimpleImputer` (numeric median, categorical most-frequent).
- Scaling for numeric features (`StandardScaler`) and one-hot encoding for categoricals.

### Models
- **Logistic Regression** (regularized)
- **Decision Tree** (tuned)
- **Random Forest** and **Gradient Boosting** as baselines

### Evaluation
- **ROC AUC** for ranking quality
- Confusion Matrix & **Classification Report**
- **Threshold tuning** to trade precision vs. recall (domain-dependent)

---

## Results (test set)
Threshold = 0.50. Metrics shown for the **positive class (Arrest = 1)** unless noted.

| Model               | ROC AUC | Accuracy | Precision | Recall | F1   |
|---------------------|:------:|:--------:|:---------:|:------:|:----:|
| **Logistic Regression** | **0.8878** | 0.8900   | 0.8100    | 0.5600 | 0.6600 |
| Random Forest       | 0.8775 | 0.8900   | 0.7900    | 0.5600 | 0.6600 |
| Gradient Boosting   | 0.8742 | 0.8900   | 0.8700    | 0.5100 | 0.6400 |
| Decision Tree       | 0.7532 | 0.8400   | 0.5800    | 0.6100 | 0.5900 |

**Observations**
- **Logistic Regression** has the best ROC AUC (0.8878) and ties Random Forest on F1 at the default 0.50 threshold.
- Gradient Boosting has the highest precision (0.87) but lower recall (0.51), indicating a stricter decision boundary.
- If your goal is to catch more arrests (higher recall), consider **lowering the threshold** for LR or adding **class weights** / resampling.

*Note:* The dataset is **class-imbalanced** (~**19%** of records are arrests in 2016). At the default 0.50 threshold, recall is conservative; lowering the threshold or using class weights can raise recall at the cost of precision.

---

## Business / Policy Takeaways
- **Resource Allocation:** A calibrated probability of arrest can highlight contexts where outcomes are more/less likely and inform staffing or escalation policies.
- **Transparency:** Logistic Regression’s coefficients support explainability for stakeholders.
- **Caveat:** Correlation ≠ causation. Always review with domain experts and consider fairness/ethics.

---

## Repository Structure
chicago-crime-ml/
├─ data/
│  ├─ raw/           # original CSV (ignored)
│  └─ processed/     # small demo sample
├─ notebooks/
│  └─ arrest_prediction_modeling.ipynb
├─ src/              # optional: pipeline code
└─ README.md, requirements.txt, .gitignore

---

## Credits
This analysis began as a **group project**. This repository reorganizes and documents the work for portfolio use, and all code/results here were re-run and validated by me.