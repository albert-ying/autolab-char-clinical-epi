---
name: clinical-trial-design
description: Clinical trial design principles. Sample size calculation, endpoint selection, randomization, CONSORT reporting.
metadata:
    skill-author: Albert Ying
---

# Clinical trial design

## When to use

- Designing a clinical study or trial
- Selecting primary/secondary endpoints
- Sample size and power calculations
- Writing study protocols and CONSORT diagrams

## Sample size calculation

```python
from scipy import stats
import numpy as np

def sample_size_two_proportions(p1, p2, alpha=0.05, power=0.80):
    """Two-sided test for difference in proportions."""
    z_alpha = stats.norm.ppf(1 - alpha / 2)
    z_beta = stats.norm.ppf(power)
    p_bar = (p1 + p2) / 2
    n = ((z_alpha * np.sqrt(2 * p_bar * (1 - p_bar))
          + z_beta * np.sqrt(p1 * (1 - p1) + p2 * (1 - p2))) ** 2
         / (p1 - p2) ** 2)
    return int(np.ceil(n))

# Example: detect 15% vs 25% response rate
n = sample_size_two_proportions(0.15, 0.25)
print(f"Need {n} per arm ({2*n} total)")
```

## Endpoint selection

| Type | Example | When to use |
|------|---------|-------------|
| Primary | Overall survival | Gold standard, requires large N |
| Surrogate | PFS, ORR | Faster readout, FDA may accept |
| Composite | MACE | Increases event rate, needs clinical plausibility |
| PRO | QoL scores | Patient-centered, increasingly valued |

## Checklist

1. Pre-register the analysis plan (clinicaltrials.gov).
2. Define primary endpoint before unblinding.
3. Account for dropout in sample size (inflate by 10-20%).
4. Stratify randomization by key prognostic factors.
5. Report per CONSORT guidelines.
