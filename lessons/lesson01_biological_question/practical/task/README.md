# Lesson 01 — From Biological Question to Machine Learning Problem: Exercises

**Section 1 of _Practical Machine Learning for Transcriptomics in Cancer Research_.**

## Overview

You will take the course's running clinical question — *predicting recurrence risk in
HR+/HER2− early breast cancer from tumour transcriptomics* — and turn it into a well-posed
machine learning problem. The practical centres on the **METABRIC** cohort combined with an
external **GEO** cohort (GSE6532) on a different array platform, so a genuine batch effect is
present for you to discover.

## Prerequisites

- Python 3.9+
- `numpy`, `pandas`, `matplotlib`, `scikit-learn`

```bash
pip install numpy pandas matplotlib scikit-learn
```

## Exercises

Open `notebooks/lesson01_exercises.ipynb` and work through the tasks.

### Task 1 — Explore the transcriptomic dataset
- Load the expression matrix from `datasets/` and inspect its shape (samples × genes).
- Note the high-dimensional, *p ≫ n* structure and what it implies for modelling.

### Task 2 — Inspect clinical metadata and define the label
- Examine the clinical table; derive the **binary recurrence endpoint** from survival follow-up.
- Quantify class imbalance and document the horizon you chose (this is the deliberate
  simplification revisited in Section 5).

### Task 3 — Identify confounders and batch effects
- Run PCA on the combined cohorts and colour points by cohort/platform.
- Show that the dominant variance axis is the **batch**, not biology.

### Task 4 — Train / validation / test splits
- Create stratified splits that preserve the recurrence class balance.
- Explain why preprocessing and feature selection must **not** be fit on validation/test data.

### Task 5 — Reflection
1. What makes this a hard prediction problem beyond "just run a classifier"?
2. Where could information leakage enter this pipeline?
3. What did framing recurrence as a binary label cost you?

## Deliverable

A **data-readiness memo** (200–300 words, Section 6 of the notebook) that doubles as your project
specification: the question, the data, the label definition (incl. censoring policy), the
confounders/batch identified, and your train/validation/test splitting strategy. Is this dataset fit
for building a *trustworthy* recurrence biomarker, and what would you fix or request before any
modelling?

## Submission

Save your completed notebook as `lesson01_exercises_<your_name>.ipynb` and submit it
according to the course instructions.
