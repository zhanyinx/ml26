# Lesson 01 — Introduction to Machine Learning: Exercises

## Overview

In this practical session you will explore a real dataset end-to-end, applying
the ML workflow introduced in the theory notes.

## Prerequisites

- Python 3.9+
- `numpy`, `pandas`, `matplotlib`, `scikit-learn`

Install with:

```bash
pip install numpy pandas matplotlib scikit-learn
```

## Exercises

Open `notebooks/lesson01_exercises.ipynb` and work through the tasks below.

### Task 1 — Exploratory Data Analysis (EDA)

- Load the provided dataset from `datasets/`.
- Inspect shape, data types, and missing values.
- Compute summary statistics (mean, std, min, max).
- Plot histograms for each feature and a correlation heatmap.

### Task 2 — Train / Validation / Test Split

- Split the dataset into training (70 %), validation (15 %), and test (15 %) sets.
- Verify that the class distribution is preserved (stratified split).
- Explain why we do **not** fit any preprocessing on the validation or test sets.

### Task 3 — Build a Baseline Model

- Train a simple majority-class classifier (or constant-value predictor) as a
  baseline.
- Evaluate it on the validation set and report appropriate metrics.

### Task 4 — Evaluate a K-Nearest Neighbours Classifier

- Train a KNN classifier (start with k = 5).
- Evaluate on validation set using accuracy and confusion matrix.
- Plot accuracy vs. k (k ∈ {1, 3, 5, 10, 20}) and discuss the bias-variance
  trade-off.

### Task 5 — Reflection

Answer the following questions in a markdown cell:

1. What does a high training accuracy but low validation accuracy indicate?
2. How would you decide between two models with similar validation accuracy
   but different training times?
3. What additional steps would you take before deploying a model?

## Submission

Save your completed notebook as `lesson01_exercises_<your_name>.ipynb` and
submit it according to the course instructions.
