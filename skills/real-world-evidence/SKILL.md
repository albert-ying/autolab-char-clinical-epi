---
name: real-world-evidence
description: Observational study methods. Propensity score matching, inverse probability weighting, target trial emulation.
metadata:
    skill-author: Albert Ying
---

# Real-world evidence

## When to use

- Analyzing observational data (EHR, claims, registries)
- Estimating treatment effects without randomization
- Propensity score matching or weighting
- Target trial emulation from longitudinal data

## Propensity score matching

```python
from sklearn.linear_model import LogisticRegression
from sklearn.neighbors import NearestNeighbors
import numpy as np

# Estimate propensity scores
ps_model = LogisticRegression(max_iter=1000)
ps_model.fit(X_covariates, treatment)
ps = ps_model.predict_proba(X_covariates)[:, 1]

# 1:1 nearest-neighbor matching (caliper = 0.2 * SD of logit PS)
logit_ps = np.log(ps / (1 - ps))
caliper = 0.2 * logit_ps.std()

treated_idx = np.where(treatment == 1)[0]
control_idx = np.where(treatment == 0)[0]

nn = NearestNeighbors(n_neighbors=1, metric="euclidean")
nn.fit(logit_ps[control_idx].reshape(-1, 1))
distances, indices = nn.kneighbors(logit_ps[treated_idx].reshape(-1, 1))

# Apply caliper
matched = distances.flatten() < caliper
matched_treated = treated_idx[matched]
matched_control = control_idx[indices.flatten()[matched]]
```

## Balance diagnostics

Always check covariate balance after matching/weighting:
- Standardized mean differences < 0.1 for all covariates.
- Variance ratios between 0.5 and 2.0.
- Visual inspection via Love plots.

## Key biases to address

- **Confounding**: propensity scores, IPW, or regression adjustment.
- **Immortal time bias**: align time zero correctly.
- **Selection bias**: define clear eligibility criteria.
- **Measurement error**: sensitivity analysis for misclassification.
