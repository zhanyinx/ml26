# Lesson 04 — Improving and Validating Models: Exercises

**Section 4 of _Practical Machine Learning for Transcriptomics in Cancer Research_.**

## Overview

You will try to improve predictive performance **while maintaining scientific rigour**:
alternative model families, principled hyperparameter optimisation, batch correction,
model interpretation, and reproducibility safeguards.

## Prerequisites

- Python 3.9+
- `numpy`, `pandas`, `matplotlib`, `scikit-learn`
- Not required: `shap` (interpretation) and `combat`/`inmoose` (batch correction) are
  discussed in the lecture but are **not** used in the practical — the notebook relies only on
  scikit-learn (RF feature importances and a hand-rolled ComBat-style correction).

```bash
pip install numpy pandas matplotlib scikit-learn
```

## Exercises

Open `notebooks/lesson04_exercises.ipynb` and work through the tasks.

### Task 1 — Alternative model families
- Compare the Lesson 02 Logistic Regression baseline against Random Forest and Gradient Boosting.
  (Decision trees are covered in the lecture as the conceptual building block of forests, but are
  not fitted as a separate model here.)

### Task 2 — Hyperparameter optimisation with nested CV
- Tune with Grid/Random search inside an outer CV loop for a less-optimistic, honest performance
  estimate that includes the cost of tuning.

### Task 3 — Batch correction
- Apply a ComBat-style location/scale batch correction (a documented stand-in for ComBat/RUV) and
  evaluate whether correction improves cross-cohort generalisation.

### Task 4 — Interpretation
- Use model feature importance (with bootstrap stability) to interpret the best model and connect top
  features to biology. (SHAP is discussed in the lecture but is not used in this practical.)

### Task 5 — Reflection
1. Did the more complex models actually beat the regularised baseline? At what cost?
2. Why does nested CV give a less optimistic estimate than tuning on a single split?
3. What reproducibility safeguards (seeds, pipelines, tracking) did you put in place?

## Deliverable

A **model comparison report** with tuned models, batch-correction results, and interpretation.

## Submission

Save your completed notebook as `lesson04_exercises_<your_name>.ipynb` and submit it
according to the course instructions.
