# Lecture 2 — Student Pack
## Building the First Predictive Model

*Practical Machine Learning for Transcriptomics in Cancer Research*

These notes accompany Lecture 2 and the second practical. Read them **after** the lecture as a study aid, and **before** the practical as a prep guide. They don't replace attending — the discussion and the live overfitting demo are where the ideas stick — but everything you need to revise and to get started is here.

This pack has three parts:

1. **Lecture handout** — the ideas, in prose, in the order they were taught.
2. **Practical prep & setup guide** — what you'll need, the data, and what you'll do.
3. **Revision summary** — a condensed recap plus self-test questions.

A reminder on the running theme: the whole course follows one real clinical problem — **predicting risk of recurrence in HR+/HER2− early breast cancer from tumour transcriptomics**. Lecture 1 framed the problem and the data; this lecture builds the first complete model on top of that clean, split, leak-aware dataset. But note the asymmetry you saw in class: the *model* took about ten minutes, and everything around it took the rest. That is the point. The single sentence to carry out of today:

> **A well-validated baseline model is more valuable than a sophisticated model with poor methodology.**

---

# Part 1 — Lecture Handout

## 1. The machine learning pipeline

A model is one stage of five: **Data → Preprocessing → Model → Prediction → Evaluation**. Most of your attention naturally goes to the model box; most *failures* happen in the other four — in preprocessing, in evaluation, or in the split.

The split discipline from Lecture 1 wraps the whole pipeline: preprocessing and model fitting are **train-only** operations, living on the training side of the wall. Walk one METABRIC patient through it: her raw expression comes in → is standardised into features → the model outputs a probability (say 0.73) → that is thresholded into "high risk" → and counted against what actually happened to her, in the evaluation. The pipeline isn't plumbing; the ordering of these boxes *is* the methodology.

## 2. What "training a model" actually means

A model is just a **function** from gene-expression features (X) to a predicted probability of recurrence. **Training** = choosing that function's parameters so its predictions fit the training data. **Prediction** = freezing the function and applying it to new patients.

The recurring danger: a function flexible enough to **memorise** the specific training patients — their noise and quirks — instead of learning reproducible biology. Training finds *a* function that fits *this* sample; whether it captured real biology is an empirical question we answer with validation, not an assumption.

## 3. The task today, restated

Input: a patient's expression profile (the wide X from Lecture 1). Output: the probability of **recurrence within the horizon** (binary y). We train on the training split, tune on validation, and don't touch the test set until the end.

**A reminder carried from Lecture 1:** this binary label is a deliberate simplification of time-to-event data — it discards *when* a relapse happened and forces censoring decisions. Fine for learning to build a baseline; we pay that debt back in Section 5 with a survival model. And be precise: on METABRIC we predict **recurrence** (a prognostic endpoint) — *not* neoadjuvant treatment response, and there is no pCR here.

## 4. Why preprocess at all?

Raw expression isn't model-ready. Genes live on wildly different scales, arrays carry technical variation, and some values are missing. A model — especially a regularised linear one — can be misled by **scale alone**: a high-variance gene can dominate the fit for reasons that have nothing to do with biology (think of a housekeeping gene at huge raw intensity swamping a lowly-expressed but important transcription factor).

Preprocessing makes features comparable and well-behaved. The golden rule from Lecture 1: **every preprocessing statistic is learned on training data only**, then applied to validation/test. Preprocessing is not cosmetic — it changes which genes the model can effectively see, and done wrong it is a leakage vector.

## 5. Preprocessing for microarray transcriptomics

Four operations, with gene-expression specifics:

- **Log transformation** — microarray intensities span orders of magnitude; logs compress the range and stabilise variance. Many public matrices arrive *already* log-scaled — check before re-logging.
- **Standardisation (z-score per gene)** — centre and scale each gene so coefficients and penalties are comparable across genes.
- **Scaling ↔ regularisation** — these are linked: a penalty implicitly assumes features share a scale, so how you scale changes what the penalty "sees."
- **Missing values** — inspect the *pattern* first, then impute *inside the split* (e.g. training-set median per gene), never across train + test.

Don't standardise as a reflex; the point is comparability for *this* model, not a ritual.

## 6. Preprocessing pitfalls (a leakage refresher)

The Lecture 1 leakage warning, made concrete:

- Standardising the **whole matrix** before splitting → normalisation leakage.
- Imputing with a **global** mean → leakage.
- Ranking genes by association with outcome on **all** samples → feature-selection leakage.

The fix is always the same: **fit on train, apply to validation/test.** In tools like scikit-learn, a "pipeline" object exists precisely to make this the path of least resistance — but the discipline is conceptual, not tool-specific. Most real leakage happens in *preprocessing*, before the model is even fit.

## 7. Logistic Regression, conceptually

Logistic Regression takes a **weighted sum** of gene expression and passes it through an **S-shaped curve** to produce a probability between 0 and 1; we then threshold that into a class. Each gene gets a **coefficient**: positive nudges the predicted probability toward recurrence, negative away (e.g. high MKI67, a proliferation marker, would carry a positive coefficient). It's interpretable, fast, and — *when regularised* — hard to overfit even in p ≫ n. Don't dismiss it as "too simple": in p ≫ n with modest signal, simple linear models are frequently state-of-the-art, and complexity often just adds overfitting.

## 8. The decision boundary

With two genes the boundary is a **line**; with thousands it's a **hyperplane** we can't draw but can reason about. One side predicts recurrence, the other not; points near the line are uncertain. The **threshold** (default 0.5) is a *choice*, not a law — move it to trade the two error types. If missing a true recurrence is worse than a false alarm (it usually is), *lower* the threshold to raise recall at the cost of precision.

## 9. Why Logistic Regression is a strong baseline

It's interpretable (coefficients map to genes biologists can scrutinise), stable, fast to cross-validate, and frequently competitive. Most importantly, it's the **yardstick**: any fancier model must *beat* a well-built Logistic Regression — convincingly, on properly validated data — to earn its complexity. A baseline is not a warm-up; it often *is* the deliverable, and it always defines what success means.

## 10. Why we need regularisation

With more genes than patients, a plain Logistic Regression can fit the training data **perfectly** and fail completely on new patients (the curse of dimensionality again). **Regularisation** adds a penalty for large or numerous coefficients — it removes degrees of freedom the data can't support. The intuition is Occam's razor: prefer the simplest gene-weighting consistent with the data.

The counterintuitive part: regularisation usually makes **training** performance *worse* and **test** performance *better*. That trade — give up fitting training noise, gain generalisation — is the whole mechanism, and it's the single most important tool for making linear models work in transcriptomics.

## 11. LASSO — sparse, feature-selecting models

LASSO drives many coefficients **exactly to zero**, leaving a small panel of genes — automatic feature selection, attractive for a candidate biomarker. The caveat (ties to Lecture 1's "signatures are not unique"): among **correlated** genes, LASSO tends to keep one arbitrarily and zero the rest, so the selected panel is **unstable** across resamples. A LASSO panel is a *hypothesis*, not a proven causal set; the genes it selects are *a* sufficient predictive set in this sample, not "the important genes."

## 12. Elastic Net — handling correlated genes

Genes act in correlated pathways — exactly the case LASSO handles poorly. **Elastic Net** blends two penalties so it can keep *groups* of correlated genes together, giving more stable, reproducible signatures across resamples. Conceptually it is an attractive default baseline for transcriptomics — Logistic Regression + Elastic Net. (It is not "LASSO with extra steps" — the grouping behaviour is qualitatively different and matters because gene features are correlated.) In the practical we keep things concrete by comparing **L1 (LASSO-style) vs L2 regularisation** separately, which already exposes the sparsity-vs-stability trade-off that Elastic Net is designed to balance.

## 13. Evaluation: accuracy is not enough

Recurrence is a **minority class**, so accuracy misleads. A model that predicts "no recurrence" for everyone can post high accuracy and be clinically worthless. Concretely: if ~22% of patients recur (as in our HR+/HER2− cohort), "always negative" scores **~78% accuracy** and **0% recall** — it identifies no one at risk. We need metrics that respect *which* errors we make, not just how many.

## 14. The confusion matrix and its metrics

From the confusion matrix come:

- **Recall (sensitivity)** — of patients who *truly* recur, how many did we flag (how few did we *miss*).
- **Precision** — of patients we *flagged*, how many truly recur (how little we *over-treat*).
- **F1** — the balance of the two.

Which to prioritise depends on the clinical cost asymmetry from Lecture 1: missing a recurrence and raising a false alarm are not equally bad. Precision and recall are not interchangeable — they answer different clinical questions and trade off against each other.

## 15. ROC-AUC and PR-AUC

Both summarise performance across **all** thresholds:

- **ROC-AUC** — the probability the model ranks a random recurrer above a random non-recurrer; 0.5 chance, 1.0 perfect. But it is **optimistic under class imbalance**, because it rewards handling the abundant negatives (the easy part).
- **PR-AUC** — precision against recall; **far more informative when positives are rare**, because it focuses on the hard, clinically important class.

Report **both**, and choose the operating threshold deliberately. A ROC-AUC near 0.8 does *not* guarantee a clinically useful model — it can hide poor precision on the very patients you're trying to find.

## 16. Why we cross-validate

A single train/validation split in a small cohort is a **noisy ruler** — your estimate depends on who happened to land in validation. **Cross-validation** rotates the held-out fold and averages, giving a more stable estimate *and* a sense of its variability (the spread across folds). It still obeys Lecture 1's rules: preprocessing and feature selection go *inside each fold*. One caution: cross-validation **measures** generalisation honestly; it does not by itself *prevent* overfitting — regularisation does that. It's the thermometer, not the medicine.

## 17. Cross-validation done right in p ≫ n

The classic silent error: select genes (or scale) using **all** the data, *then* cross-validate — the CV score is already contaminated and looks far too good. The correct pattern refits the **entire pipeline** (impute → scale → select → fit) inside every fold. Add the two omics-specific constraints from Lecture 1: **stratify** folds to preserve the recurrence minority, and use **patient-level** folds if any patient has multiple samples.

The trap to remember: cross-validation wrapped around a leaky pipeline doesn't catch the leak — it **launders** it into a confident, stable-looking, wrong number. That's worse than a single bad split, because it looks so trustworthy.

## 18. Overfitting — the core danger

Overfitting = great on training, poor on new data — memorising patients, not biology. In p ≫ n it is the **default**, not the exception; regularisation and honest validation are our two defences. **Diagnose** it by comparing training vs validation/test performance: a large gap is the fingerprint. (Training AUC 0.99, cross-validated AUC 0.62 → the model is telling you it memorised the training patients.) Overfitting doesn't mean the model is bad — it means *trusting the training number* would mislead you; the fix is method, not abandoning the model.

## 19. Watching overfitting happen

As you weaken regularisation (let the model get more complex), **training performance keeps climbing** while **validation performance rises, peaks, then falls**. The peak is the sweet spot; past it, the model fits noise. This is the **bias–variance trade-off**, taught here by intuition rather than equations — and you'll reproduce this exact curve on METABRIC in the practical. If training is still climbing but validation has started to drop, you've passed the peak: increase regularisation.

## 20. Interpreting the model (carefully)

Coefficients rank genes by contribution — a legitimate starting point for biology, and a reason we like linear baselines. But carry forward every Lecture 1 caution: **predictive ≠ causal**; regularised coefficients are *selected*, so read them as **hypotheses**; check biological plausibility (proliferation up, ER signalling down) and **stability** across folds. A big coefficient is not automatically an important gene — magnitude depends on scaling and on which correlated partners were selected. And ask the Lecture 3 question: does a top proliferation gene add anything over tumour grade, a cheap clinical variable?

## 21. Why the baseline is the point (the main message, again)

A baseline is the scientific anchor of the project. It's the standard later models are judged against; it surfaces data problems (leakage, imbalance, batch) early, before complexity hides them; and if a simple, well-validated model already works, that is a **result**, not a disappointment. If your Elastic Net baseline hits CV AUC 0.70 and a neural net hits 0.71 on the same split, prefer the interpretable, stable baseline — a one-point within-cohort difference is almost certainly noise.

> **A well-validated baseline model is more valuable than a sophisticated model with poor methodology.**

---

# Part 2 — Practical Prep & Setup Guide

## What you'll need

- **Python 3.9+** with Jupyter, plus `numpy pandas scikit-learn matplotlib`. The notebook reuses Lecture 1's loaders, which auto-install `GEOparse` if needed.
- **Internet access** the first time you run it — the practical reuses the **same METABRIC data cache as Lecture 1**, so if you ran Lecture 1's practical the download is already there and is reused (not re-fetched).
- The notebook from the repo: `lessons/lesson02_baseline_model/practical/task/notebooks/lesson02_exercises.ipynb`.

## The dataset (so you arrive oriented)

You continue with the prepared **METABRIC HR+/HER2−** cohort from Lecture 1: the samples × genes expression matrix, the binary **recurrence** label (relapse within the horizon, with the documented censoring policy), and the **patient-level, stratified** train/validation/test split. The notebook loads these prepared artefacts (or regenerates them, documented, if absent). Remember: recurrence is the **minority class**, so accuracy will mislead and you must stratify everything.

## What you'll do, section by section

The practical builds one complete, trustworthy baseline — and makes you *feel* leakage and overfitting rather than just hear about them.

0. **Setup & framing** — load the prepared data; confirm shapes, class balance, and split integrity.
1. **Confirm the starting point** — re-verify alignment, zero patient overlap, and preserved class proportions. (Never trust saved artefacts blindly.)
2. **Preprocessing inside a pipeline** — build impute → scale as fit-on-train-only; then the **leakage trap**: scale on the whole matrix first and *measure* the optimism it injects.
3. **Train a regularised Logistic Regression** — compare weak vs strong regularisation (training only), and count how many genes a LASSO-style model keeps versus a dense one.
4. **Evaluate with the right metrics** — confusion matrix, precision/recall/F1, ROC-AUC and PR-AUC; contrast with the trivial "always negative" classifier; move the threshold and justify a choice.
5. **Cross-validation, done honestly** — stratified k-fold with the whole pipeline inside each fold; then the **leakage hunt**: build a leaky CV (select genes on all data, then CV) and measure the gap against honest CV.
6. **Train vs test, and watching overfitting** — produce the overfitting-gap bars and the regularisation sweep curve; find the validation sweet spot.
7. **Interpret the coefficients** — rank top genes, check plausibility and stability across resamples.
8. **Baseline model report** — your written deliverable.

## How to approach it

- **Predict before you run.** At each "Discussion" prompt, write your guess first (which model generalises better? which metric matters?). The leakage and overfitting cells are designed to surprise you — let them.
- **Watch the leakage cells closely.** Sections 2 and 5 deliberately do the *wrong* thing so you can see the optimism inflate, then deflate it. That gap is the lesson.
- **Don't chase the number.** The goal is a *defensible* baseline, not the highest AUC. A modest, honest, stable result beats an impressive, leaky one.

## The deliverable

A **200–300 word "baseline model report"** (Section 8): what the baseline achieves (with the right metric and its uncertainty from CV), how it was validated, where it might be overfitting, what the coefficients suggest as *hypotheses*, and what you'd need before trusting it as a recurrence biomarker.

---

# Part 3 — Revision Summary

## The ten things to remember

1. **The model is one of five stages.** Data → Preprocessing → Model → Prediction → Evaluation; most failures happen *outside* the model box, and the split wraps the whole pipeline.
2. **Preprocessing is a leakage vector.** Fit every statistic (scaling, imputation, selection) on **training data only**; a pipeline object enforces this.
3. **Logistic Regression** outputs a probability via an S-curve; coefficients are gene contributions (+ toward recurrence, − away). Interpretable, fast, strong *when regularised*.
4. **Regularisation** trades worse *training* performance for better *test* performance — the key tool in p ≫ n. **LASSO** → sparse panel (unstable across correlated genes); **Elastic Net** → keeps correlated groups, more stable.
5. **Accuracy misleads under imbalance.** "Always no-recurrence" scores high accuracy and zero recall.
6. **Precision vs recall:** precision = don't over-flag; recall = don't miss a relapser. They trade off; choose by clinical cost.
7. **ROC-AUC flatters under imbalance; PR-AUC is more honest** when positives are rare. Report both; the threshold is a choice.
8. **Cross-validation** gives a stable estimate + its spread — but only if the **whole pipeline refits inside each fold**. CV around a leaky pipeline *launders* the leak.
9. **Overfitting is the default in p ≫ n:** great on training, poor on new data. Diagnose via the train-vs-test gap; the regularisation sweep shows validation peaking then falling.
10. **The baseline is the yardstick.** Interpret coefficients as hypotheses (predictive ≠ causal); a well-validated simple model beats a fancy model on a compromised pipeline.

## Key terms

- **Pipeline** — preprocessing + model bundled so all data-dependent steps refit on training data only (and inside each CV fold).
- **Regularisation** — a penalty on large/numerous coefficients that reduces overfitting.
- **LASSO / Elastic Net** — regularisers that select features; Elastic Net keeps correlated groups together.
- **Precision / recall / F1** — metrics from the confusion matrix; recall = sensitivity.
- **ROC-AUC / PR-AUC** — threshold-free ranking summaries; PR-AUC is preferred under class imbalance.
- **Cross-validation (stratified, k-fold)** — rotating held-out folds, preserving class proportions, to estimate generalisation.
- **Overfitting gap** — the difference between training and validation/test performance; the fingerprint of memorisation.
- **Bias–variance trade-off** — too simple underfits, too complex overfits; the validation peak is the sweet spot.

## Self-test (try before checking back against Part 1)

1. A model reports 0.83 accuracy on a cohort where 18% recur, but recall 0.35. Why is the accuracy nearly uninformative, and which metric matters more here? *(§13–15)*
2. A student z-scores every gene across the whole dataset, then splits into train/test, then fits Logistic Regression. Where is the leak, and how do you fix it? *(§4, §6, §17)*
3. Why does adding regularisation usually make *training* performance worse but *test* performance better? *(§10)*
4. You run 10-fold CV but selected your 100 "best" genes on all the data beforehand. Why is the resulting AUC untrustworthy — and why is it *especially* dangerous? *(§17)*
5. Training AUC is 0.99 and cross-validated AUC is 0.62. What is happening, and what do you change? *(§18–19)*
6. LASSO gives you a different 12-gene panel each time you resample patients. Is the method broken? What does this tell you about interpreting the genes? *(§11, §20)*
7. Your top coefficient is a known proliferation gene. Is that reassuring — and what's the catch? *(§20)*

## One sentence to leave with

> **A well-validated baseline model is more valuable than a sophisticated model with poor methodology** — the pipeline, the honest evaluation, and the overfitting vigilance are what make a model trustworthy, not the cleverness of the classifier.
