# Organic Agriculture Survey Data Imputation

**Goal:** Improve data completeness in agricultural field experiments by applying machine learning–based imputation and NLP-assisted name cleaning.  
**Techniques:** K-Nearest Neighbors (KNN), XGBoost regression, iterative imputation, fuzzy string matching.

## Project Context
This project was part of a larger randomized controlled trial (RCT) studying the adoption and impact of organic crop fertilizer among rural households.  
During the baseline and follow-up surveys, we found:
- **>20% missingness** in household empowerment question
- **Inconsistent household names** due to enumerator spelling variations

Our objective:
1. Minimize information loss from missing data  
2. Standardize household names for accurate panel matching  
3. Ensure reproducibility for future survey-based research

---

## Approach
### Missing Data Imputation
We tested:
- **KNNImputer** from `scikit-learn`
- **XGBoost** regression
- **Iterative imputation** (similar to MICE)
- Benchmarked against mean/median imputation

KNN performed best for our dataset size and structure, balancing accuracy and speed.

**Example (synthetic data):**
```python
import pandas as pd
from sklearn.impute import KNNImputer

df = pd.DataFrame({
    'fertilizer_use_kg': [100, None, 80, None, 60],
    'yield_kg': [300, 250, None, 200, None],
    'rainfall_mm': [120, 100, 90, None, 110]
})

imputer = KNNImputer(n_neighbors=2)
df_imputed = pd.DataFrame(imputer.fit_transform(df), columns=df.columns)
print(df_imputed)
