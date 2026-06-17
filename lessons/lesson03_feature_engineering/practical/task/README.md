# Lesson 03 — Feature Engineering and Biological Representation: Exercises

**Section 3 of _Practical Machine Learning for Transcriptomics in Cancer Research_.**

## Overview

This is the core section of the course: **feature representation often matters more than
model selection**. You will build several feature sets from the same expression data and
compare how well each predicts recurrence — and you will see how improper feature selection
silently invalidates a study through information leakage.

## Prerequisites

- Python 3.9+
- `numpy`, `pandas`, `matplotlib`, `scikit-learn`
- `gseapy` is **not required** — the pathway scoring here uses a simple in-notebook stand-in (optional only if you later want real ssGSEA/GSVA)

```bash
pip install numpy pandas matplotlib scikit-learn
```

## Exercises

Open `notebooks/lesson03_exercises.ipynb` and work through the tasks.

### Task 1 — Gene-level features
- Compare raw expression, variance-filtered genes, and differentially expressed genes.

### Task 2 — Biological signatures
- Compute proliferation, estrogen-signaling, immune and stromal scores; use them as features.

### Task 3 — Pathway-level features
- Score Hallmark-like gene sets per sample using a simple ssGSEA/GSVA-style score (mean of
  standardised member genes) as a stand-in, and use the pathway activities as features.
  (Real `gseapy`-based ssGSEA/GSVA is optional and not required here.)

### Task 4 — Feature selection without leakage
- Embed selection (variance thresholding + univariate / differential-expression `SelectKBest`)
  **inside** the CV pipeline so it refits within each fold.
- Demonstrate the inflated AUC when selection is (wrongly) done on the full dataset first.
- *(Conceptual only — covered in the lecture, not practised here: LASSO / Elastic-Net and
  tree-based feature importance.)*

### Task 5 — Reflection
1. Which feature representation generalised best, and why might that be biologically sensible?
2. By how much did leaky feature selection inflate the apparent AUC?
3. When are pathway-level features preferable to gene-level features?

## Deliverable

A **feature engineering notebook** comparing the representations with leakage-free evaluation.

## Submission

Save your completed notebook as `lesson03_exercises_<your_name>.ipynb` and submit it
according to the course instructions.
