---
name: survival-analysis
description: Time-to-event analysis with scikit-survival. Kaplan-Meier, Cox regression, random survival forests, concordance index.
metadata:
    skill-author: Albert Ying
---

# Survival analysis

## When to use

- Time-to-event data with censoring
- Kaplan-Meier curves and log-rank tests
- Cox proportional hazards modeling
- Survival prediction and evaluation (C-index, Brier score)

## Kaplan-Meier

```python
from sksurv.nonparametric import kaplan_meier_estimator
import matplotlib.pyplot as plt

for group in df["treatment"].unique():
    mask = df["treatment"] == group
    time, surv = kaplan_meier_estimator(
        df.loc[mask, "event"].astype(bool),
        df.loc[mask, "time"],
    )
    plt.step(time, surv, where="post", label=group)
plt.xlabel("Time (months)")
plt.ylabel("Survival probability")
plt.legend()
```

## Cox proportional hazards

```python
from sksurv.linear_model import CoxPHSurvivalAnalysis
from sksurv.preprocessing import OneHotEncoder
import numpy as np

y = np.array(
    [(bool(e), t) for e, t in zip(df["event"], df["time"])],
    dtype=[("event", bool), ("time", float)],
)
X = df[["age", "stage", "biomarker"]].copy()

cph = CoxPHSurvivalAnalysis(alpha=0.01)
cph.fit(X, y)

# Concordance index
from sksurv.metrics import concordance_index_censored
c_index = concordance_index_censored(y["event"], y["time"], cph.predict(X))
print(f"C-index: {c_index[0]:.3f}")
```

## Assumptions to check

- **Proportional hazards**: Schoenfeld residuals, log-log plots.
- **Linearity**: Martingale residuals for continuous covariates.
- **Informative censoring**: censored patients should not differ systematically from observed.
