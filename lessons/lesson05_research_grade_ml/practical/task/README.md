# Lesson 05 — Research-Grade Machine Learning: Mini-Project

**Section 5 of _Practical Machine Learning for Transcriptomics in Cancer Research_.**

## Overview

The final section is about what distinguishes a *publishable* machine learning study from an
exploratory analysis. This capstone is a **review–diagnose–repair** project built around three
conceptual goals: (1) a *reviewer simulation* on a deliberately flawed biomarker study, (2)
rebuilding it as a complete, *reproducible, externally-validated pipeline*, and (3) a
*binary-vs-survival discussion* of the course's central modelling choice — then writing it all up
as a research-style report.

This is also where that central modelling choice is examined honestly: throughout Sections 1–4
recurrence was treated as a **binary label**; here you articulate what that discarded and why the
real **ER-Predict** assay (Boscolo Bielo et al., *ESMO Open* 2026) — developed on the same METABRIC
cohort — is built on a **time-dependent survival core** (a survival SVM plus a gradient-boosting
survival forest), with a binary classifier kept only as a tiebreaker when the two survival models
disagree.

## Prerequisites

- Python 3.9+
- `numpy`, `pandas`, `matplotlib`, `scikit-learn`
- A second, independent cohort for external validation

## Exercises

Open `notebooks/lesson05_exercises.ipynb`. You are handed a *deliberately flawed* "submitted
analysis" claiming an impressive recurrence biomarker, and you work through seven parts (Part 0 is
shared setup that loads the prepared METABRIC + GSE6532 data). The three conceptual goals map onto
the parts as follows.

**Reviewer simulation (Parts 1–3, 6).**

- **Part 1 — Review the provided analysis.** Run the authors' analysis, reproduce its headline
  number (e.g. AUC ≈ 0.9), and restate its claim precisely.
- **Part 2 — Identify the methodological flaws.** Audit it against the reviewer checklist and *name*
  each flaw (feature-selection leakage, no external validation, incorrect preprocessing, tuning
  optimism, unsupported interpretation); demonstrate the leakage with a **permuted-label** check.
- **Part 3 — Propose corrections.** Build a flaw → fix → predicted-effect table (most fixes should
  *lower* the headline number).
- **Part 6 — Write a reviewer report.** A structured review with a publish / minor / major / reject
  recommendation. **This is the primary assessable artefact.**

**Reproducible, externally-validated pipeline (Part 4, compared in Part 5).**

- **Part 4 — Build a corrected workflow.** Selection/scaling/correction inside CV, nested CV, an
  honest baseline, a stability-checked interpretation, a genuine **external validation** on GSE6532,
  and a clinical-baseline comparison. **Part 4.4** covers selection stability and the optional
  survival framing.
- **Part 5 — Compare original vs corrected.** Quantify how much of the headline was real signal vs
  leakage + tuning optimism + the absence of external validation.

**Binary-vs-survival discussion (Part 4.4, revisited in Part 7).**

- In and around **Part 4.4**, answer: *We could have run the whole course as binary classification —
  what specifically would a reviewer say we lost, and in which clinical situations would that loss
  change a conclusion?* This is where the **ER-Predict** survival core (above) is the worked
  exemplar.
- **Part 7 — Reflection.** Which course lessons (L1–L5) mattered most.

> **Scope note:** the course does not teach survival-analysis mechanics (hazard functions,
> partial likelihood). The goal is to *recognise* when a binary endpoint throws away information
> and to *know* that time-to-event methods exist and were the right choice for the real model.

## Deliverable

A **research-style report** plus a **reproducible notebook**. Assessment focuses on methodology,
validation strategy, biological interpretation and reproducibility — not predictive performance alone.

## Submission

Save your completed notebook as `lesson05_exercises_<your_name>.ipynb` and submit it with your
report according to the course instructions.
