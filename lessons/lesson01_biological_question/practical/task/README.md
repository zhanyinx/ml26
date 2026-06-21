# Lesson 01 — From Biological Question to Machine Learning Problem

**Section 1 of _Practical Machine Learning for Transcriptomics in Cancer Research_.**

## Overview

No modelling yet. This session is about *knowing your data before you trust it*: you take the
course's running clinical question — *predicting recurrence risk in HR+/HER2− early breast cancer
from tumour transcriptomics* — and turn it into a well-posed machine-learning problem. You load a
real transcriptomic dataset, audit its clinical metadata, define a clean label, measure class
balance, run PCA, discover a batch effect, and build a defensible train/validation/test split.

The practical centres on **METABRIC**, combined with an external **GEO** cohort (GSE6532) on a
different array platform, so a genuine cross-platform batch effect is present for you to discover.

## Setup

Install the course environment once from the repository root (see the top-level
[`README.md`](../../../../README.md) and [`requirements.txt`](../../../../requirements.txt)):

```bash
pip install -r requirements.txt
```

On first run the notebook **downloads its data** (internet required): METABRIC from the cBioPortal
datahub (expression matrix ~660 MB) and GSE6532 from NCBI GEO (~180 MB). Data is cached under
`datasets/` (git-ignored) and **shared with the later lessons**, so it is downloaded only once. The
first download takes a few minutes.

## What you'll do

Open `notebooks/lesson01_exercises.ipynb` and work through its numbered sections in order; each ends
in a short written answer or a figure.

- **Explore the dataset** — load the expression matrix, confirm its orientation (samples × genes),
  and note the high-dimensional *p ≫ n* structure and what it implies for modelling.
- **Define the label** — derive the **binary recurrence endpoint** from survival follow-up, document
  the horizon and censoring policy, and quantify class imbalance (the deliberate simplification
  revisited in Lesson 5).
- **Find confounders & batch effects** — run PCA on the combined cohorts, colour by platform vs
  recurrence, and show that the dominant variance axis is the **batch**, not the biology.
- **Design the splits** — build a patient-level, stratified, batch-aware train/validation/test
  split, and pinpoint where preprocessing and feature selection must (and must not) be fit.

## Deliverable

A **data-readiness memo** (200–300 words; the final section of the notebook) that doubles as your
project specification: the question, the data, the label definition (incl. censoring policy), the
confounders/batch identified, and your splitting strategy. Is this dataset fit for building a
*trustworthy* recurrence biomarker, and what would you fix or request before any modelling?

## Submission

Save your completed notebook as `lesson01_exercises_<your_name>.ipynb` and submit it
according to the course instructions.
