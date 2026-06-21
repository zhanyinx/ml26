# Practical Machine Learning for Transcriptomics in Cancer Research

## Course Overview

### Course Title

**Practical Machine Learning for Transcriptomics in Cancer Research: From Biological Question to Research-Grade Predictive Model**

### Audience

PhD students in Computational Biology, Bioinformatics, Translational Oncology, and related fields.

### Duration

* **Lectures:** 7–8 hours
* **Practical Sessions:** 12–15 hours
* **Structure:** 5 sections combining theory, hands-on exercises, and a final project

---

# Course Philosophy

Most machine learning courses focus on algorithms.

This course focuses on the process of developing a predictive model from biological data.

The goal is not to train machine learning researchers.

The goal is to train computational biologists capable of building, evaluating, interpreting, and critically reviewing predictive models derived from transcriptomic data.

The central message of the course is:

> Building a trustworthy predictive biomarker is more important than choosing a sophisticated algorithm.

---

# Running Case Study

The entire course revolves around a single translational oncology problem:

**Predicting risk of recurrence in HR+/HER2− early breast cancer from tumour microarray transcriptomic data.**

The course uses **METABRIC** (Pereira et al. 2016, via cBioPortal) as its primary cohort — the development cohort of a recently published HR+/HER2− recurrence model (ER-Predict, Boscolo Bielo et al., *ESMO Open* 2026) that the course follows as a real-world exemplar. For the introductory practical, METABRIC is combined with an external GEO cohort (Loi et al., GSE6532) on a different array platform so that a genuine batch effect is present for students to discover. The prediction label is a **binary recurrence endpoint** derived from long-term survival follow-up (relapse/DMFS-style), since METABRIC carries no neoadjuvant pCR endpoint. The course treats this binary simplification as a deliberate, documented choice and revisits its cost when the research-grade survival model is introduced in Section 5.

Students will progressively construct a complete machine learning pipeline:

1. Problem formulation
2. Data exploration
3. Baseline modeling
4. Feature engineering
5. Feature selection
6. Model comparison
7. Validation
8. Interpretation
9. Research-style reporting

By the end of the course, students will have built a publication-style predictive model and learned how to identify common methodological mistakes in biomarker studies.

---

# Learning Objectives

By the end of the course, students should be able to:

### Biological Understanding

* Formulate a biological prediction problem as a machine learning task
* Understand the challenges of transcriptomic datasets
* Interpret predictive features in a biological context

### Machine Learning Practice

* Build a complete supervised learning workflow
* Create and evaluate baseline predictive models
* Perform feature engineering and feature selection
* Compare alternative modeling strategies

### Research Methodology

* Avoid information leakage
* Recognize overfitting
* Design appropriate validation strategies
* Assess model reproducibility
* Critically evaluate machine learning studies in the biomedical literature

---

# Section 1

# From Biological Question to Machine Learning Problem

### Lecture (1.5 h)

The course begins with the clinical and biological motivation behind predictive modeling in oncology.

Topics include:

* Precision medicine and recurrence-risk prediction
* The HR+/HER2− breast cancer case study
* Structure of transcriptomic datasets
* Features and labels
* High-dimensional biology
* Batch effects
* Class imbalance
* Data leakage
* Train/validation/test splitting

### Practical (2–3 h)

Students:

* Explore a transcriptomic dataset
* Inspect clinical metadata
* Identify confounders
* Investigate batch effects using PCA
* Create train/validation/test splits

### Deliverable

Project specification document.

---

# Section 2

# Building the First Predictive Model

### Lecture (1.5 h)

Students are introduced to baseline predictive modeling using interpretable methods.

Topics include:

* Transcriptomic preprocessing
* Logistic Regression
* Regularized models
* Cross-validation
* Classification metrics
* Overfitting

Emphasis is placed on understanding model behavior rather than algorithmic complexity.

> **Thread to keep alive (1 min):** the baseline model here is a *binary* classifier (relapsed vs not, by a fixed horizon). Remind students this is the simplification flagged in Section 1 — convenient for learning the workflow, but it discards the timing of relapse and forces censored patients out of the analysis. Promise the payoff: in Section 5 they will see the research-grade model keep the time axis with a survival model, and they will be able to articulate what binary framing cost them.

### Practical (3 h)

Students:

* Train a baseline Logistic Regression model
* Evaluate performance using cross-validation
* Compare training and testing performance
* Interpret evaluation metrics

### Deliverable

Baseline model report.

---

# Section 3

# Feature Engineering and Biological Representation

### Lecture (2 h)

This is the core section of the course.

Students learn that feature representation often matters more than model selection.

Topics include:

### Gene-Level Features

* Raw expression values
* Variance filtering
* Differential expression

### Biological Signatures

* Proliferation signatures
* Estrogen signaling scores
* Immune signatures
* Stromal signatures

### Pathway-Level Features

* Gene sets
* Hallmark pathways
* GSVA
* ssGSEA

### Feature Selection

* Variance thresholding
* Correlation filtering
* LASSO
* Elastic Net
* Tree-based importance

### Information Leakage

Students learn how improper feature selection can invalidate entire studies.

### Practical (3 h)

Students compare models using:

* All genes
* Variance-filtered genes
* Differentially expressed genes
* Biological signatures
* Pathway activity scores

### Deliverable

Feature engineering notebook.

---

# Section 4

# Improving and Validating Models

### Lecture (1.5 h)

Students learn how to improve predictive performance while maintaining scientific rigor.

Topics include:

### Alternative Models

* Decision Trees
* Random Forests
* Gradient Boosting

### Hyperparameter Optimization

* Grid Search
* Random Search
* Nested Cross-Validation

### Batch Correction

* ComBat
* Removal of unwanted variation

### Model Interpretation

* Feature importance
* SHAP values
* Biological interpretation

### Reproducibility

* Pipelines
* Random seeds
* Experiment tracking

### Practical (3 h)

Students:

* Compare multiple model families
* Tune hyperparameters
* Evaluate batch correction strategies
* Interpret predictive features

### Deliverable

Model comparison report.

---

# Section 5

# Research-Grade Machine Learning

### Lecture (1 h)

The final section focuses on what distinguishes a publishable machine learning study from an exploratory analysis.

Topics include:

### End-to-End Workflow

Question → Data → QC → Baseline → Feature Engineering → Validation → Interpretation → Publication

### External Validation

* Independent cohorts
* Cross-study reproducibility
* Model transportability

### From Binary Labels to Time-to-Event (the payoff)

Throughout Sections 1–4 the course deliberately treated recurrence as a **binary label** (relapsed vs not, by a fixed horizon). This is the section where that choice is examined honestly, using the course's real research-grade model as the worked example.

* **What binary framing discards.** (1) *Timing* — a relapse at 11 months and one at 110 months carry the same label despite very different biology and prognosis. (2) *Censored patients* — those event-free but with follow-up shorter than the horizon cannot be cleanly labelled and are usually dropped, shrinking the cohort and biasing it toward longer-followed patients. (3) *Horizon-dependence* — every binary result is contingent on an arbitrary cutoff, and conclusions can swing when the cutoff moves.
* **What a time-dependent (survival) model keeps.** It uses both *whether* and *when*, and it *retains* censored patients by modelling their partial information rather than discarding them. No fixed horizon is required.
* **The course's real model as exemplar.** The published ER-Predict assay the course follows is built on a time-dependent survival core (a survival SVM and a gradient-boosting survival forest); a time-independent classifier appears only as a tiebreaker when the two survival models disagree. This is a concrete illustration that the research-grade choice was *time-dependent*, with binary classification relegated to a supporting role.
* **Discussion / exercise framing.** Ask students: "We could have run the whole course as binary classification — what specifically would a reviewer say we lost, and in which clinical situations would that loss change a conclusion?" Strong answers connect the discarded timing/censoring to a concrete failure (e.g. a model that looks prognostic at 5 years but says nothing about the late recurrences that dominate HR+/HER2− disease — exactly the gap the running paper targets).
* **Boundary of scope.** The course does not teach survival analysis mechanics (hazard functions, partial likelihood, etc.); the goal is for students to *recognise* when a binary endpoint is throwing away information and to *know* that time-to-event methods exist and were the right choice for the real model. Deeper survival modelling is signposted as a follow-on, not delivered here.

### Common Reviewer Criticisms

* Information leakage
* Weak validation
* Circular feature selection
* Overinterpretation

### Future Directions

* Deep learning for transcriptomics
* Foundation models in biology
* Multi-omics integration

### Practical (2–3 h)

Students complete a mini-project and participate in a reviewer simulation exercise.

They must identify flaws in a deliberately problematic biomarker study and then develop their own complete predictive pipeline.

### Deliverable

Research-style report and reproducible notebook.

---

# Assessment Strategy

### Continuous Assessment

* Practical notebooks
* Short written reports
* Discussion questions

### Final Project

Students submit:

* Reproducible notebook
* Research-style report
* Presentation of results

Assessment focuses on:

* Correct methodology
* Validation strategy
* Biological interpretation
* Reproducibility

rather than predictive performance alone.

---

# What the Course Deliberately Does Not Cover

The course minimizes emphasis on:

* Statistical learning theory
* VC dimension
* Optimization proofs
* Kernel derivations
* Neural network mathematics
* Advanced theoretical machine learning

These topics are less relevant for the practical challenges faced by computational biology researchers.

---

# Key Takeaways

Students completing this course should understand that:

1. Machine learning starts with a biological question.
2. Transcriptomic data present unique challenges.
3. Feature engineering often matters more than algorithm selection.
4. Data leakage can invalidate seemingly impressive results.
5. Validation is more important than optimization.
6. Biological interpretation requires caution.
7. A successful biomarker must be reproducible, interpretable, and clinically meaningful.

The ultimate goal is to enable students to build predictive models that could withstand peer review and serve as the foundation for translational cancer research.
