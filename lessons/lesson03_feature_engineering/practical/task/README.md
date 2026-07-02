# Lesson 03 — Feature Engineering and Biological Representation: Exercises

**Section 3 of _Practical Machine Learning for Transcriptomics in Cancer Research_.**

## Overview

This is the core section of the course: **feature representation often matters more than
model selection**. You will build several feature sets from the same expression data and
compare how well each predicts recurrence — and you will see how improper feature selection
silently invalidates a study through information leakage.

## Setup

Run **Lesson 0** once (before any practical) to install the environment and populate the shared data
cache: [`lessons/lesson00_prerequisites/`](../../../lesson00_prerequisites/). This notebook **loads the checkpoint saved by the previous lesson** (it does not re-derive the cohort); if that
checkpoint is missing it raises with a pointer to run the earlier lesson first. Run **Lesson 0** once
to create the `ml26` conda environment and download the data.
`gseapy` is **not** required — the pathway scoring here uses a simple in-notebook stand-in (mean of
standardised member genes); real `gseapy`-based ssGSEA/GSVA is optional if you want it later.

## What you'll do

Open `notebooks/lesson03_exercises.ipynb` and work through its numbered sections in order. It is one
controlled experiment: hold the model fixed and vary only the feature representation.

- **Gene-level features** — compare raw expression, variance-filtered genes, and
  differentially-expressed genes.
- **Biological signatures** — build proliferation, ER-signalling, immune and stromal scores and use
  the handful of scores as features.
- **Pathway-level features** — score Hallmark-like gene sets per sample and model the pathway
  activities; check stability versus raw genes.
- **Feature selection without leakage** — embed selection **inside** the CV pipeline so it refits
  within each fold, then *measure* how much a leaky select-on-all-data step inflates the AUC — even
  on permuted labels. *(LASSO/Elastic-Net and tree-based importance are covered conceptually in the
  lecture, not practised here.)*
- **Reflection** — which representation generalised best and why; how much leakage inflated the AUC;
  when pathway-level features beat gene-level ones.

## Deliverable

A **feature engineering notebook** comparing the representations with leakage-free evaluation.

## Submission

Save your completed notebook as `lesson03_exercises_<your_name>.ipynb` and submit it
according to the course instructions.
