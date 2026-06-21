# Lesson 04 — Improving and Validating Models: Exercises

**Section 4 of _Practical Machine Learning for Transcriptomics in Cancer Research_.**

## Overview

You will try to improve predictive performance **while maintaining scientific rigour**:
alternative model families, principled hyperparameter optimisation, batch correction,
model interpretation, and reproducibility safeguards.

## Setup

Install the course environment once from the repository root (see the top-level
[`README.md`](../../../../README.md) and [`requirements.txt`](../../../../requirements.txt)):

```bash
pip install -r requirements.txt
```

This practical reuses the **shared METABRIC cache** from Lesson 1 (expression matrix ~660 MB); if
you haven't run Lesson 1 it downloads on first run. **Internet access is required the first time.**
`shap` and `combat`/`inmoose` are discussed in the lecture but are **not** used here — the notebook
relies only on scikit-learn (RF feature importances and a hand-rolled ComBat-style correction).

## What you'll do

Open `notebooks/lesson04_exercises.ipynb` and work through its numbered sections in order.

- **Alternative model families** — compare the Lesson 2 Logistic Regression baseline against Random
  Forest and Gradient Boosting. (Decision trees appear in the lecture as the conceptual building
  block of forests, but aren't fitted as a separate model here.)
- **Hyperparameter optimisation with nested CV** — tune inside an outer CV loop for an honest
  estimate that includes the cost of tuning.
- **Batch correction** — apply a ComBat-style location/scale correction (a hand-rolled stand-in for
  ComBat/RUV) to a **simulated** cross-platform cohort, judge whether correction helps, and see where
  doing it on pooled data leaks.
- **Interpretation** — use feature importance (with bootstrap stability) to interpret the best model
  and connect top features to biology. (SHAP is discussed in the lecture, not used here.)
- **Reflection** — did the complex models beat the regularised baseline, and at what cost? Why is
  nested CV less optimistic? What reproducibility safeguards did you put in place?

## Deliverable

A **model comparison report** with tuned models, batch-correction results, and interpretation.

## Submission

Save your completed notebook as `lesson04_exercises_<your_name>.ipynb` and submit it
according to the course instructions.
