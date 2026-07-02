# Lecture 4 — Student Pack
## Model Improvement, Validation, and Interpretation

*Practical Machine Learning for Transcriptomics in Cancer Research*

These notes accompany Lecture 4 and the fourth practical. Read them **after** the lecture as a study aid, and **before** the practical as a prep guide. They don't replace attending — the discussion and the live nested-CV and batch-correction demos are where the ideas stick — but everything you need to revise and to get started is here.

This pack has three parts:

1. **Lecture handout** — the ideas, in prose, in the order they were taught.
2. **Practical prep & setup guide** — what you'll need, the data, and what you'll do.
3. **Revision summary** — a condensed recap plus self-test questions.

A reminder on the running theme: the whole course follows one real clinical problem — **predicting risk of recurrence in HR+/HER2− early breast cancer from tumour transcriptomics**. Lectures 1–3 built a trustworthy problem, a trustworthy model, and trustworthy features. This lecture is where you stop *building* models and start *judging* them — comparing families, tuning honestly, validating across cohorts, and interpreting without overreach. The single sentence to carry out of today:

> **Better validation is usually more important than a better algorithm.**

---

# Part 1 — Lecture Handout

## 1. When is a baseline not enough?

You already own a regularised Logistic Regression, biological features, and honest cross-validation. The interesting question isn't *"can I build something fancier?"* — of course you can — it's *"can I demonstrate the fancier thing is genuinely better?"* Complexity carries a **burden of proof**: a more complex model must *earn* its complexity with a validated, meaningful gain, or it's just a liability (more tuning, less interpretability, more overfitting risk) for nothing. When a collaborator says "my deep model beats your baseline by 0.02 AUC," ask three questions: is 0.02 outside the cross-validation noise? Did they tune honestly? Did it hold on an external cohort? The excitement usually evaporates. Retire the reflex that *more complex = more powerful = better*; more complex means more capacity to overfit and to fool yourself, unless the gain survives honest validation.

## 2. How do we know a model is "better"?

Compare on the **same split, same metrics, same validation** — change *only* the model (the controlled-experiment logic of Lecture 3, now applied to algorithms). The key statistical humility: a difference that sits *within* cross-validation fold-to-fold noise is **not an improvement**. Two models at CV AUC 0.71 ± 0.05 vs 0.72 ± 0.06 are a *tie* — and a tie goes to the simpler, more interpretable model. Don't dress up noise as a discovery; most honest comparisons show smaller gaps than the literature implies.

## 3. Decision trees

A decision tree is a cascade of yes/no rules ("is proliferation score > x? if yes, is ER signalling < y?") down to risk groups at the leaves. It reads like clinical decision-making, which makes a single tree highly **interpretable**. Two caveats: single trees are **unstable** (resample the patients → a completely different tree, Lecture 1's non-uniqueness again), and they **overfit** easily without depth/leaf-size constraints. The deep lesson: *interpretability without stability is a trap* — readability is not reliability. That instability motivates forests.

## 4. Random Forests

Grow many trees, each on a bootstrap resample of patients and a random subset of features, and let them **vote/average**. The magic is **variance reduction**: the trees' errors are partly independent and cancel on averaging, while the shared signal survives — the same logic as aggregating genes into a signature (Lecture 3). A forest gives a feature-importance ranking "for free" (heavily caveated — §13). Limits: less interpretable than one tree; importances biased and unstable; **not magic on small n** — it can't manufacture signal that isn't there. Expect it to roughly *match* the regularised baseline on our data, not leap ahead. And note: forests overfit *less* than a single deep tree, but they can still overfit.

## 5. Gradient Boosting

Build trees **sequentially**, each correcting the previous ensemble's errors. Often the strongest tabular predictor and a frequent benchmark winner — but it is the **easiest to overfit** and the **most tuning-sensitive**. In p ≫ n with modest biological signal, its edge over a good baseline is usually *small*. Boosting wins Kaggle competitions with 100,000 rows and strong signal; our problem is a few hundred patients with modest signal and heavy noise — the regime where its advantage shrinks and its overfitting risk grows. State-of-the-art *on large, signal-rich tabular data* is not the same as the right choice here.

## 6. Which model family, when?

Regularised linear: interpretable, stable, strong in p ≫ n — **the default**. Random Forest: robust, low-tuning, good when interactions matter, weaker interpretability. Gradient Boosting: highest ceiling, highest overfitting risk, needs careful tuning + validation. **Write this down:** in transcriptomics, complex models *rarely* beat a good biologically-informed baseline by much — the choice is usually about robustness and interpretability, not a big accuracy jump. Default to regularised LR on good features; escalate only if interactions clearly matter *and* you can validate the gain.

## 7. Parameters vs hyperparameters

**Parameters** are learned *from* the data during fitting (gene coefficients, tree split thresholds). **Hyperparameters** are set *before* fitting and control the model (tree depth, number of trees, penalty strength C). You don't learn hyperparameters by fitting — you **search** for good values, and that search must be validated honestly. (Number of trees: hyperparameter. The genes a tree splits on: parameters.) Tuning is a *search over models*, and it consumes validation honesty in a way ordinary fitting does not — the seed of the field's most common validation error.

## 8. Grid and random search

**Grid search**: try every combination on a predefined grid — thorough but expensive, and it wastes effort on bad regions. **Random search**: sample combinations at random — often finds good values *far more cheaply*, especially when only a few hyperparameters matter. The non-negotiable rule: every candidate is scored by **cross-validation, never the final test set**. Cost explodes combinatorially — each hyperparameter multiplies the search, and every fit is itself a cross-validation, so costs reach thousands of fits fast. (More exhaustive ≠ more rigorous.)

## 9. The danger hiding in tuning

Every time you tune to the validation score, you "use up" a little of its honesty. Tune *hard* and the best score you find becomes **optimistic** — you've overfit the *tuning process* to that validation data. The counterintuitive part: the model never saw the test set, yet your *estimate* is still inflated, because you **selected** the configuration *using* those scores (like buying 1,000 lottery tickets and reporting the winner's prize as your expected return). The fix can't be "be more careful" — it must be **structural**: nested cross-validation.

## 10. Cross-validation review

k-fold CV rotates the held-out fold and averages; everything data-dependent (scaling, selection) refits *inside* each fold (Lectures 2–3). But this estimates performance for a **fixed** modelling choice. If the choice itself — the hyperparameters — was selected using the data, plain CV is no longer honest. Cross-validation gives an honest estimate *only for a pre-specified model*; the moment you tune, it becomes optimistic.

## 11. Nested cross-validation

Two loops. The **inner** loop selects hyperparameters (it's allowed to look and choose). The **outer** loop estimates performance — and each outer fold is held out from everything the inner loop did, so it judges the *finished, tuned model* on data that played no part in selecting it. The outer estimate therefore **includes the cost of tuning** and doesn't inflate. One cycle: outer holds out fold 1; on folds 2–5 the inner CV picks the best hyperparameters and refits; predict fold 1; repeat for each outer fold; average. It's expensive (a CV inside a CV) but it's the correct way to report a tuned model. *It's not "more folds" — it's separating selection from estimation.*

## 12. Why nested CV matters

Without nesting, tuned models report optimistic scores that don't replicate — and the inflation is worst exactly where we work (small n, many hyperparameters, flexible models). The same tuned model might give 0.76 on flat CV and 0.69 nested — that gap *is* tuning optimism. **The rule to carry forever:** *if you tuned on it, you cannot also report performance on it.* When a paper reports a single CV AUC for a model whose hyperparameters were "optimised," ask whether the optimisation was nested — if not, the number is optimistic.

## 13. Why models fail on new cohorts

A model can look excellent in-cohort and **collapse** on an independent dataset. The usual culprit isn't a bad model — it's **technical variation between cohorts** (batch effects). Our setting: METABRIC (Illumina) vs GSE6532 (Affymetrix), a real cross-platform batch effect (in **this** practical, reproduced with a *simulated* GSE6532-like cohort — held-out METABRIC patients with an injected batch effect; see Part 2). A signature at 0.78 internal CV can drop to 0.60 externally — the model latched onto platform-specific intensities that don't transfer. **Cross-cohort validation is the closest thing to truth before a clinical trial.** Good in-cohort CV says *nothing* about cross-platform transfer; only external validation does.

## 14. Batch effects, revisited

Systematic *technical* differences by platform, site, date, or protocol — not biology. Dangerous when **larger than the signal**, and far worse when **correlated with the outcome** (confounding), because then the model can "predict" outcome by reading the batch. PCA **diagnoses** batch (do samples cluster by platform rather than biology?) but does *not* fix it. This is the single biggest reason transcriptomic biomarkers fail to transfer. (Don't "just remove PC1" — it may carry real biology too.)

## 15. Batch correction: ComBat and friends

ComBat and "remove unwanted variation" methods estimate and subtract batch-associated shifts so cohorts become comparable; done well, cross-platform models can work. **Risks:** aggressive correction can *remove real biology* (if a true biological difference aligns with the batch structure, it gets subtracted too), and done wrong it can *leak the outcome* (§16). A gentler partial alternative (Lecture 3): **pathway-level features** are more platform-robust by design, because aggregation averages out probe-specific quirks — sometimes a better representation beats a heavier correction. Batch correction is a *modelling choice with real risks*, not a neutral clean-up step.

## 16. Where batch correction belongs in the pipeline

**Cardinal rule:** correction must not use the outcome label, and must respect the split — **estimate it on training data, apply the same transform to validation/test**. Correcting train + test *together* (on all data, before splitting) is a **leakage vector**: even though ComBat is unsupervised, the *transform* borrows information across the split (test samples influence the correction applied to training and vice versa), so folds are no longer independent and the estimate inflates. And the structural caveat: if batch is **perfectly confounded** with outcome (e.g. all recurrers on one platform), *no* method can rescue it — only better study design could have.

## 17. Interpreting models responsibly

Three tools — linear coefficients, tree-based importance, SHAP — all answer one question: *what is the model using?* **None** answers *what causes recurrence?* Interpretation is for hypothesis generation and sanity-checking (is the model leaning on plausible biology or a batch artefact?), not causal claims. The single greatest source of overreach in biomarker papers is the interpretation step, where "the model uses gene X" silently becomes "gene X drives the disease."

## 18. Feature importance and coefficients (and their traps)

Linear coefficients: direction + magnitude, but depend on scaling and on which correlated partner was selected. Random-forest importance: ranks usefulness for splitting, but is biased and unstable. The deep issue: **correlated genes share/split importance unpredictably**, so a "top" gene can be an artefact of correlation (two correlated proliferation genes — a forest may hand most importance to one almost at random). Always check **stability** across folds/resamples before believing a ranking; report the stable features, treat unstable ones with suspicion. Importance is not biological importance.

## 19. SHAP values (conceptual)

SHAP attributes each *individual* prediction to its features — a consistent per-patient explanation (e.g. high proliferation pushed this patient's risk up; high ER signalling pushed it down). Useful for showing *how the model decided* for one tumour, and for catching when a model leans on something it shouldn't. **Limit:** SHAP explains the *model*, not the *biology* — a faithful explanation of a flawed/confounded model is still flawed. Never present SHAP as evidence of mechanism or causation.

## 20. Prediction is not causation

A predictive feature can be a **true driver**, an **innocent correlate** (tracks proliferation → grade), or a **technical artefact** (a batch probe) — all three look *equally predictive*, and the model can't tell them apart. Predictive value can be genuinely real and useful *without* any causal role — that's fine, but you must say it's **predictive, not causal**. (ESR1 predicts lower recurrence risk; does it *cause* better outcomes, or mark luminal-A-like biology? The model can't tell you.) Causal claims need interventional or carefully-designed causal evidence — never model weights. *Plausibility + prediction ≠ causation.*

## 21. Reproducibility

**Random seeds:** fix and report them — results that change run-to-run aren't results. **Pipelines:** encapsulate preprocessing + model so the whole thing refits inside CV (prevents leakage, makes the analysis portable). **Version control + environment capture:** same code + data + versions → same number. **Notebook organisation + experiment tracking:** every figure and metric regenerable from a known state. It's ordinary scientific hygiene — the computational equivalent of a documented wet-lab protocol. In biomarker research the stakes are concrete: irreproducible signatures have reached the clinic and harmed patients (Lecture 1's Duke/Potti). A result you can't regenerate is an **anecdote**, not a finding — and reproducibility is **self-defense**, catching your own leakage and bugs before a reviewer (or a patient) does.

## 22. Choosing the best model

The best model is **not** the most complex, nor the highest training (or even CV) score in isolation. The best model is **robust, interpretable, reproducible, and well-validated**. Decision rule: *prefer the simplest model whose advantage survives honest validation* — nested CV for the tuning, cross-cohort for the transfer. For a clinic-bound biomarker, trust and transferability beat a fractional AUC gain. (If gradient boosting beats your baseline by 0.015 nested-CV AUC but drops below it externally and you can't explain its features — publish the baseline, and report the boosting result honestly as not improving generalisation.) Choosing a model is a *judgement* across four axes, not an AUC-maximisation problem.

> **Better validation is usually more important than a better algorithm** — most gains are smaller than expected, validation errors do more damage than model choice, and biological plausibility does not prove causality.

---

# Part 2 — Practical Prep & Setup Guide

## What you'll need

- **Python 3.9+** with Jupyter, plus `numpy pandas scikit-learn matplotlib`. The notebook reuses the earlier loaders (auto-installing `GEOparse` if needed) and the Lecture 3 engineered features; batch correction uses a ComBat-style routine.
- **Internet access** the first time — the practical reuses the **same METABRIC cache as Lectures 1–3**, so prior downloads are reused. The external cohort for Part 8 is a **synthetic GSE6532-like** stand-in built in-notebook (no separate download).
- The notebook: `lessons/lesson04_model_validation/practical/task/notebooks/lesson04_exercises.ipynb`.

## The dataset (so you arrive oriented)

You continue with the prepared **METABRIC HR+/HER2−** cohort, the binary **recurrence** label, the **patient-level, stratified** split, and — crucially — the **engineered features from Lecture 3** (biological signatures + pathway scores). For the batch-correction experiment you'll use a **synthetic GSE6532-like external cohort** — held-out METABRIC patients with a deliberately injected batch effect (scale/shift plus technical noise), standing in for an independent cohort on a different platform because the real cross-platform GSE6532 data is not bundled with the notebook. Recurrence is the minority class; the binary label is a deliberate simplification of time-to-event data (paid back in Lecture 5).

## What you'll do, part by part

This practical is a fair, honest **model bake-off** — and a lesson in the validation that keeps it honest.

1. **Load engineered features** — and define one reusable `evaluate(model, X)` helper (ROC-AUC, PR-AUC, recall, precision). Only the model changes.
2. **Logistic Regression** — the baseline every other model must clear; record its *per-fold spread*, not just the mean.
3. **Random Forest** — compare to the baseline *including spread*: is any difference outside the noise?
4. **Gradient Boosting** — expose its overfitting tendency (train vs CV gap).
5. **Hyperparameter tuning** — grid or random search on one model, then **nested CV** on the same procedure; **measure the optimism gap** (flat best-CV minus nested).
6. **Model comparison** — assemble the dashboard (metrics + a stability measure) with fold-to-fold error bars.
7. **Feature importance** — rank top features, check biological plausibility *and stability*; write the predictive-not-causal note.
8. **Batch-correction experiment** — train on METABRIC, test on GSE6532: (a) no correction, (b) correction estimated on train and applied to the external cohort, and (the wrong way) (c) correcting both together before splitting. Compare, and try pathway-feature transfer too.
9. **Reflection** — choose a model "for publication" and justify it.

## How to approach it

- **Predict before you run.** Before Part 5, guess how big the nested-vs-flat gap will be. Before Part 8, guess whether the fancy model transfers. Let the results surprise you.
- **Watch Parts 5 and 8 closely.** The nested-CV optimism gap and the batch-correction experiment are the heart of the practical. Part 8(c) deliberately does correction the *wrong* (pooled) way so you can *measure* the inflation before it's explained.
- **Expect deflation, and embrace it.** The tuned complex models likely do *not* convincingly beat the baseline once nested CV and external validation are applied. That deflation **is** the lesson — it's a success, not a disappointment.
- **Model the reproducibility lesson:** fix and report a seed, keep everything inside pipelines, make every number regenerable.

## The deliverable

A **200–300 word model-selection statement** (Part 9): which model you'd choose *for publication* and why, weighing internal CV, nested-CV honesty, external-cohort transfer, stability, interpretability, and reproducibility — and explicitly stating whether any complex model's advantage survived honest validation.

---

# Part 3 — Revision Summary

## The ten things to remember

1. **Validation > algorithm.** Better validation usually matters more than a better algorithm; most gains are smaller than expected, and validation errors do more damage than model choice.
2. **A difference within CV noise is not an improvement.** Compare on the same split/metrics/validation; a tie goes to the simpler, interpretable model.
3. **Trees → forests → boosting:** single trees are interpretable but unstable; forests reduce variance by averaging; boosting is powerful but the easiest to overfit. Complex models rarely beat a good baseline by much in p ≫ n.
4. **Parameters are learned; hyperparameters are chosen** (and searched for, then validated honestly).
5. **Tune by grid/random search, scoring on CV — never the test set.** Random search is often cheaper for the same performance.
6. **Tuning inflates estimates** even though the model never saw the test set, because you *selected* the configuration using the scores. The fix is structural.
7. **Nested CV** separates selection (inner) from estimation (outer): *if you tuned on it, you can't also report performance on it.*
8. **Models fail on new cohorts** mainly because of **batch effects**; in-cohort CV doesn't predict cross-platform transfer — external validation does.
9. **Batch correction** must be estimated on training only and applied to held-out data (pooling train+test leaks even though it's unsupervised); if batch is confounded with outcome, no method rescues it.
10. **Interpretation ≠ causation.** Coefficients/importance/SHAP explain the *model*, not the biology; check stability; predictive value can be real without being causal. Reproducibility (seeds, pipelines, versions) is non-negotiable.

## Key terms

- **Decision tree / Random Forest / Gradient Boosting** — a single rule-cascade; many averaged trees (variance reduction); sequentially error-correcting trees (powerful, overfit-prone).
- **Parameter vs hyperparameter** — learned from data during fitting vs set before fitting and searched for.
- **Grid / random search** — exhaustive vs sampled hyperparameter search, scored by CV.
- **Nested cross-validation** — inner loop selects hyperparameters, outer loop estimates performance honestly.
- **Tuning optimism** — inflation of a reported score caused by selecting the configuration on the same data.
- **Batch effect / batch correction (ComBat)** — systematic technical variation between cohorts / methods to estimate and subtract it (estimated on train, applied to test).
- **Feature importance / SHAP** — rankings/attributions of what the *model* uses; not causal evidence.
- **Reproducibility** — fixed seeds + encapsulated pipelines + version/environment capture so every result is regenerable.

## Self-test (try before checking back against Part 1)

1. Model B's mean CV AUC is 0.72 vs the baseline's 0.71, but the per-fold spreads fully overlap. Have you shown an improvement? What would you do before claiming one? *(§2)*
2. Why might averaging 500 unstable trees beat one carefully-grown tree? *(§4)*
3. Is the penalty strength C a parameter or a hyperparameter? What about a gene's fitted coefficient? *(§7)*
4. How can hyperparameter tuning inflate a CV estimate even though the model never saw the test set? *(§9)*
5. In nested CV, which loop is allowed to "see" the data to make choices, and which must stay blind — and why? *(§11)*
6. A student runs ComBat on the combined METABRIC + GSE6532 matrix, then splits and cross-validates. What's the error, and why does it inflate performance even though ComBat is unsupervised? *(§16)*
7. Your model's top SHAP feature is a proliferation gene. What can and can't you conclude, and name two non-causal reasons it could be highly predictive. *(§19–20)*
8. Gradient boosting beats your baseline by 0.015 nested-CV AUC but drops below it externally and you can't explain its features. Which do you publish, and how do you frame it? *(§22)*

## One sentence to leave with

> **Better validation is usually more important than a better algorithm** — the better model is the one you can *prove*, *transfer*, and *explain*, and proving it is the real science.
