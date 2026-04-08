# Organic Agriculture RCT — Data Cleaning and ML Imputation Pipeline

**Project context:** Randomized controlled trial on organic farming technology
adoption among rural households in the Philippines
**Role of this pipeline:** Prepare baseline and follow-up survey data for
causal analysis, including gender-disaggregated labor productivity estimation
**Keywords:** RCT data cleaning, KNN imputation, XGBoost, fuzzy matching,
field survey data, reproducible pipeline

---

## Why this matters

Field survey data from low-resource settings is rarely clean. This RCT
collected data from hundreds of farm households across multiple rounds,
with enumerator-introduced spelling variation in household names and
>20% item-level missingness in key empowerment and labor variables.

Sloppy handling of either problem: imputing badly or failing to match
panel records correctly would bias the treatment effect estimates that
the research depends on.

This repo documents the pipeline we used to solve both problems
reproducibly.

---

## Two problems, two solutions

### Problem 1: Missing values in outcome variables

Missing data was not random — it was concentrated in female labor hours
and household empowerment questions, which are harder to collect and
more sensitive. Naive listwise deletion would have disproportionately
removed female-headed and lower-wealth households, biasing the
gender analysis.

**Approach tested:**

| Method | Notes |
|---|---|
| Mean/median imputation | Baseline benchmark; distorts variance |
| KNNImputer (scikit-learn) | Best performer for our dataset size and structure |
| XGBoost regression | Strong on larger datasets; slightly overfit here |
| Iterative imputation (MICE-style) | Computationally intensive; marginal gain |

KNN performed best overall, balancing accuracy and interpretability.

### Problem 2: Inconsistent household names across survey rounds

Enumerators spelled household names differently across rounds, making
panel linkage unreliable. We used **fuzzy string matching** (RapidFuzz)
to identify likely duplicates and flag cases for manual review, with
a confidence threshold to separate automatic matches from manual QA.

---

## Notebook

`Data_cleaning-Sample.ipynb` — synthetic data example demonstrating:
- KNN imputation with cross-validation on held-out cases
- Fuzzy name matching with threshold-based QA flagging
- Comparison of imputation strategies on simulated missingness

*Original survey data is not shared due to IRB restrictions.*

---

## Transferability

The same pipeline logic applies to any administrative or clinical dataset
with panel linkage challenges and structured missingness — including
EHR records with inconsistent patient identifiers, or claims data with
missing or miscoded diagnosis codes.

---

## Author

Mengshan Zhao | mengshan.zhao@wsu.edu | [www.mengshanzhao.com](https://www.mengshanzhao.com)
