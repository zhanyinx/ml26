# Lesson 02 — Building the First Predictive Model: Exercises

**Section 2 of _Practical Machine Learning for Transcriptomics in Cancer Research_.**

## Overview

You will build the course's first baseline predictive model: an interpretable Logistic
Regression (and a regularised variant) for the **binary recurrence endpoint**. The emphasis
is on understanding model behaviour and evaluating it honestly — not on algorithmic
sophistication.

> **Thread from Section 1:** the baseline here is a *binary* classifier (relapsed vs not, by a
> fixed horizon). It discards the timing of relapse and forces censored patients out of the
> analysis. This simplification is examined honestly in Section 5.

## Setup

Run **Lesson 0** once (before any practical) to install the environment and populate the shared data
cache: [`lessons/lesson00_prerequisites/`](../../../lesson00_prerequisites/). This notebook **loads the checkpoint saved by the previous lesson** (it does not re-derive the cohort); if that
checkpoint is missing it raises with a pointer to run the earlier lesson first. Run **Lesson 0** once
to create the `ml26` conda environment and download the data.

## Exercises

Open `notebooks/lesson02_exercises.ipynb` and work through the sections.

### Section 0 — Setup & framing
- Load the prepared Lecture 1 cohort from the shared `datasets/` cache (regenerate from
  cBioPortal + GEO only if needed); orient around the binary recurrence endpoint.

### Section 1 — Load and confirm the starting point
- Verify the train/validation/test split artefacts and confirm the recurrence class balance.

### Section 2 — Preprocessing inside a pipeline
- Build a `Pipeline` (impute → scale → model) so every data-dependent step is fit on training
  data only — then walk into the **leakage trap** on purpose and quantify the optimism it buys.

### Section 3 — Train a regularised Logistic Regression
- Fit a **weakly** vs **strongly** regularised baseline and compare; observe L1/L2 behaviour
  and the resulting selected-gene (sparsity) count.

### Section 4 — Evaluate with the right metrics
- Build the full metrics table (precision, recall, F1, ROC-AUC, PR-AUC), sweep the decision
  threshold, and plot ROC and PR curves with a justified threshold choice.

### Section 5 — Cross-validation, done honestly
- Run honest stratified k-fold CV (the whole pipeline refit inside every fold) and compare it
  against the leaky alternative.

### Section 6 — Train vs test, and watching overfitting
- Measure the train-vs-CV overfitting gap and run a regularisation sweep to choose a strength.

### Section 7 — Interpret the coefficients (carefully)
- Rank the coefficients as biological *hypotheses* and check their stability across resamples.

### Section 8 — Baseline model report
- Write the assessable **200–300 word baseline model report** (the Section-2 deliverable).

## Deliverable

A **200–300 word baseline model report**: the headline metric with its CV uncertainty, the
right metric under imbalance and the chosen threshold, the train-vs-CV overfitting gap and the
regularisation chosen, the top coefficient hypotheses and their stability, and what you would
need before trusting it as a recurrence biomarker.

## Submission

Save your completed notebook as `lesson02_exercises_<your_name>.ipynb` and submit it
according to the course instructions.
