# Lecture 5 — Student Pack
## Research-Grade Machine Learning: Validation, Reproducibility, and Biomarker Development

*Practical Machine Learning for Transcriptomics in Cancer Research*

These notes accompany Lecture 5 — the **capstone** — and the final practical. Read them **after** the lecture as a study aid, and **before** the practical as a prep guide. They don't replace attending — the discussion and the live survival-framing and significance-vs-utility examples are where the ideas stick — but everything you need to revise and to start the capstone is here.

This pack has three parts:

1. **Lecture handout** — the ideas, in prose, in the order they were taught.
2. **Practical prep & setup guide** — what you'll need, the data, and what the capstone asks.
3. **Revision summary** — a condensed recap plus self-test questions.

A reminder on the running theme: the whole course follows one real clinical problem — **predicting risk of recurrence in HR+/HER2− early breast cancer from tumour transcriptomics**, training on METABRIC and validating on GSE6532. Lectures 1–4 built a trustworthy problem, model, features, and validation. This lecture is where you stop *building* models and learn to *judge* them — to recognise why most biomarkers never reach patients, and to review a study as a peer reviewer would. The single sentence to carry out of the whole course:

> **The hardest part of machine learning is not training a model. It is demonstrating that the model is trustworthy.**

---

# Part 1 — Lecture Handout

## 1. Why most biomarkers fail

Thousands of transcriptomic signatures have been published; very few are used clinically. Usually not because the biology was wrong — because the **evidence was never trustworthy**, and almost all the failures happen *after* model development. The path narrows like a funnel: thousands published → few externally validated → fewer reproducible → a handful adopted. Publication checks novelty and apparent rigour, **not** generalization or clinical utility — so "published" and "validated" are different claims, and a journal name is not a substitute for your own judgement.

## 2. The five recurring causes of failure

Overfitting (performance in one dataset's noise, L2); small cohorts (the p ≫ n curse → unstable estimates, fragile signatures, L1); dataset bias (batch/site/platform mistaken for biology, L4); no external validation (never tested where it counts); and publication bias (the optimistic results are disproportionately the ones published, so the literature itself runs high). **None of these is a bad algorithm — they are evidence failures**, unfixable by switching model family. Publication bias is the hardest to spot as a reader: it's invisible in any single paper.

## 3. Internal validation (and its limit)

The ladder from L2/L4 — train/test split → cross-validation → nested CV — each answers "how well does this *pipeline* do on data *like this*?" Done right, it controls overfitting and tuning optimism. But every rung stays **inside one cohort**. Internal validation tells you the *pipeline is honest*; it cannot tell you the *model travels*. Nested CV is not a proof the model works — only that your internal estimate isn't inflated by tuning.

## 4. External validation — the true test

Testing on a genuinely **different** cohort (different hospital, study, population, platform). It tests *generalization*, not memorization, and is the closest thing to truth before a prospective trial. Our course example: train on METABRIC (Illumina), test on GSE6532 (Affymetrix) — a real cross-platform test. A different *platform* is more honest than a random split because it forces reliance on transferable biology, not platform-specific intensities. **A held-out split of the same cohort is internal, not external.** The internal-to-external performance drop is often the single most informative number in a biomarker paper — and the one most often missing.

## 5. Why external validation is so demanding

It exposes everything internal validation hides: batch effects, cohort quirks, leakage that survived CV. Performance almost always drops; the honest paper reports *how much* and argues usefulness anyway. **"Validated" should mean externally validated.** The subtle trap: if you re-tune a signature until it "passes" one external cohort, that cohort has become a second training set — external validation is *spent* the first time you use it to make a choice. More external cohorts is only more rigorous if you don't tune on them.

## 6. Cohort and dataset shift

Models assume the deployment world looks like the training world; when it doesn't, you have **distribution shift** — the reason external performance drops (not bad luck, a measurable change in the data). Sources: different populations, treatment protocols, platforms, labs. Batch effects (L4) are one *kind* of shift; population and treatment shifts are biological/clinical and just as damaging.

## 7. Three flavours of shift

**Covariate shift** — feature distributions differ, rule holds (new platform's intensities; younger cohort). **Label shift** — outcome frequency differs (a screening cohort with far fewer recurrences). **Concept shift** — the feature→outcome relationship itself changes (a new standard-of-care therapy alters who recurs). Each needs a different response, and **concept shift can invalidate a biomarker entirely** — what it learned is no longer true, and recalibration can't fix that. (Example: adjuvant CDK4/6 inhibitors are a concept shift for a signature trained before them.)

## 8. Paying back the time: from binary label to survival

All course long we simplified time-to-event recurrence into a **binary** label — a deliberate teaching scaffold. Real outcomes are **time-to-event** with **censoring** (patients followed without an event, then lost to follow-up or still event-free at last contact). A binary label discards timing and censoring. **Survival models** (Cox, conceptually — no maths) use the time axis and handle censoring; that's the research-grade framing. The validation discipline is unchanged; the outcome is just richer. Why it matters here: HR+/HER2− disease **recurs late**, so a 5-year binary label mislabels a year-7 recurrer as a non-recurrer — hiding exactly the signal we care about. (Two "non-recurrers" at 5 years — one recurred at 5.5y, one censored at 2y — are identical to the binary label and different to a survival model.)

The course's real exemplar is the published **ER-Predict** assay (Boscolo Bielo et al., *ESMO Open* 2026), developed on the same METABRIC cohort we use. It made exactly the research-grade choice: its core is **time-dependent (survival)** — a **survival SVM** plus a **gradient-boosting survival forest** — and a time-independent **binary classifier appears only as a tiebreaker**, used when the two survival models disagree. So the binary framing we leaned on all course is, in the real model, demoted to a supporting role behind a survival core. **Scope note (unchanged):** we still don't teach survival mechanics (hazard functions, partial likelihood); the goal is to *recognise* when a binary endpoint throws away information and to *know* that time-to-event methods exist and were the right choice for the real model.

## 9. Reproducibility — two kinds

**Computational reproducibility**: someone reruns your code + data and gets your numbers. **Scientific reproducibility (replication)**: someone reproduces your *finding* in *new* data with their *own* pipeline. Different bars — you can pass the first (every number regenerates) and fail the second (a cohort-specific fluke). Biomarker research needs both; most work falls short of even the first.

## 10. What makes work reproducible

Fixed, reported **seeds**; an **encapsulated pipeline** (everything data-dependent refits inside CV → no leakage, portable); **version & environment** capture; documentation so every figure/number regenerates from a known state; and **open science** (code, data where possible, model/specification). Reproducibility is **self-defense** (L4) — it catches your own leakage before a reviewer does. When patient data can't be shared, you can still share code, the exact spec, seeds, environment, summary statistics, and synthetic/access-controlled data. It's about trust and verifiability, not generosity.

## 11. Statistical significance ≠ clinical utility

A biomarker can be highly significant and clinically useless. Significance: "an association exists." Utility: "does it change a decision and help a patient?" At large n, even a tiny effect (HR ≈ 1.05) is significant — and may move **no** decision. (A signature "significant at p < 0.001" that separates 10-year risk by 2% changes no chemotherapy decision.) A p-value reflects evidence *that* an effect exists, not its size or usefulness.

## 12. What clinical utility actually requires

**Actionability** (changes management — escalate/de-escalate/monitor); **incremental value** (beats grade, stage, nodal status, Ki-67 — the L2 idea, now central); **net benefit at a usable threshold** (helps more than it harms at a real decision point). A biomarker that doesn't beat tumour grade and isn't actionable won't — and shouldn't — be adopted. The most important, most-often-omitted comparison: **added value over standard clinical variables**. (Beating AUC 0.5 is not utility; beating the pathologist's grade might be.)

## 13. Exploratory vs confirmatory analysis

**Exploratory**: screen many features/models/thresholds — valuable, but optimistic by nature (try enough things, something looks great). **Confirmatory**: pre-specify model and endpoint, test **once** on untouched data. Most published biomarker ML is **exploratory dressed as confirmatory** — the core sin, and the biggest reason results don't replicate. Fix: a locked analysis plan (pre-registration where possible) and a test set touched exactly once. Reporting the best of 200 explored signatures is measuring *the maximum of 200 noisy draws*, not a confirmed result.

## 14. How reviewers think, and the six recurring criticisms

Reviewers check one thing: **is the claim supported by the evidence?** Most rejections are a short, avoidable list — and that list is your own pre-submission checklist. The six: **no external validation** (→ test on an independent cohort); **leakage** (→ selection/scaling/correction inside the split, in a pipeline); **weak biological interpretation** (→ coherent mechanism + stability, not a bare gene list); **circular feature selection** (→ redo selection inside each fold); **missing baselines** (→ compare to clinical variables and a simple model); **unrealistic performance** (→ explain a suspiciously high AUC in p ≫ n; be suspicious yourself). Every one is an *evidence* failure. A strong result built on any of them is exactly the one that won't replicate.

## 15. How to read an ML paper (the 2-minute interrogation)

Task & **n** (plausible for the claim?); validation (**external?**); leakage (**order of operations?**); reproducibility (code/data/seeds?); conclusions (justified, or overreaching — causation from prediction, utility from significance?). Reading critically ≠ distrusting everything; it's calibrating trust to evidence. The most diagnostic single questions: *was there genuine external validation?* and *what was the order of selection vs splitting?*

## 16. Where the field is going — and the constant

Deep learning (powerful with big data; in p ≫ n shares and amplifies every pitfall); foundation models (promising — validation rules unchanged); multi-omics integration; spatial omics (the L3 microenvironment, in situ); precision oncology (right drug, right patient). The banner over all of it: **the validation rules don't change.** New methods raise the *ceiling* on performance; they don't lower the *bar* for evidence — and bigger models in small cohorts can fail harder and more confidently. The motifs converge: *representation > algorithm* (L3), *validation > algorithm* (L4), *trust > novelty* (L5).

## 17. The end-to-end workflow

Question → Data → Baseline → Feature engineering → Model selection → Validation → Interpretation → Publication. Each stage had a lecture and a failure mode. **Trustworthiness is built in at every stage, not bolted on at the end** — a leak or mis-framed problem early poisons everything downstream. This pipeline is the course.

> **The main message:** *the hardest part of ML is not training a model; it is demonstrating that the model is trustworthy.* Most failures occur after model development; validation matters more than optimization; reproducibility is essential; skepticism is healthy. You can now build, evaluate, **and review**.

## 18. The through-line of the whole course

It helps to see how the five lectures compose into one habit of mind. Lecture 1 made the *problem* trustworthy — the right question, an honest look at the data, leakage and batch named early. Lecture 2 made the *model* trustworthy — a validated baseline and honest cross-validation, with the lesson that a well-validated baseline beats a sophisticated model with poor methodology. Lecture 3 made the *features* trustworthy — biological representation over raw genes, selection inside the fold, with the lesson that better representation often helps more than a fancier algorithm. Lecture 4 made the *improvement* trustworthy — model families compared fairly, nested CV for tuning, batch correction in its proper place, interpretation kept separate from causation, with the lesson that better validation usually matters more than a better algorithm. And Lecture 5 makes the *study* trustworthy — external validation, reproducibility, clinical utility, and the reviewer's eye, with the lesson that demonstrating trust is the hard part and the durable skill. Read top to bottom, the motifs stack: **representation > algorithm**, **validation > algorithm**, **trust > novelty** — all pointing at the same word, and all surviving whatever methods come next.

---

# Part 2 — Practical Prep & Setup Guide (the Capstone)

## What you'll need

- **Python 3.9+** with Jupyter, plus `numpy pandas scikit-learn matplotlib`. The capstone reuses the earlier loaders (auto-installing `GEOparse` if needed), the Lecture 3 engineered features, and a ComBat-style batch routine; an optional survival cell uses `lifelines` if available.
- **Internet access** the first time — the capstone reuses the **same METABRIC + GSE6532 cache as Lectures 1–4**, so prior downloads are reused.
- The notebook: `lessons/lesson05_research_grade_ml/practical/task/notebooks/lesson05_exercises.ipynb`.

## The dataset (the whole course, assembled)

You continue with the prepared **METABRIC HR+/HER2−** cohort, the binary **recurrence** label (and, optionally, its **time-to-event** version), the patient-level stratified split, the **Lecture 3 engineered features** (signatures + pathways), and the second cohort **GSE6532** for genuine **external validation**. Recurrence is the minority class; the binary label is the deliberate simplification you may now replace with a survival framing.

## What the capstone asks

This is a **review-diagnose-repair** project, not a build-from-scratch one. You are handed a *deliberately flawed* "submitted analysis" claiming an impressive recurrence biomarker, and you do what a careful reviewer (and author) must:

1. **Review the provided analysis** — run it, reproduce its impressive-looking cross-validated headline, and restate its claim precisely.
2. **Identify the flaws** — audit it against the reviewer checklist and *name* each flaw you find. The checklist spans leakage, validation, preprocessing/batch, tuning optimism, and interpretation — work out which apply here rather than assuming. Demonstrate the leakage you suspect with a **permuted-label** check.
3. **Propose corrections** — a table of flaw → fix → predicted effect (most should *lower* the number).
4. **Build a corrected workflow** — selection/scaling/correction inside CV, nested CV, an honest baseline, stability-checked interpretation, and a genuine external validation on the **real GSE6532 cohort** — validate *both* the authors' raw-gene model (it collapses: platform-specific probes don't transfer) *and* your corrected engineered-feature model (it holds: biological signatures are cross-platform robust) — plus a clinical-baseline comparison (optional: a survival framing).
5. **Compare original vs corrected** — quantify the leakage/tuning inflation, and use the external cohort to show which representation actually transfers.
6. **Write a reviewer report** — summary, major/minor issues with evidence, reproducibility and clinical-relevance assessments, and a recommendation: publish / minor revision / major revision / reject. **This is the primary assessable artefact.**
7. **Reflect** — which course lessons (L1–L5) mattered most.

## How to approach it

- **Predict before you run.** Before correcting, guess how far the headline number will fall. The gap between the flashy original and the honest corrected number **is** the lesson — and the course thesis (validation > optimization). Frame the deflation as success.
- **Let the checklist do the work.** The flaws are discoverable from the analysis itself; the reviewer checklist is the tool that surfaces them. Don't expect them to be labelled.
- **Protect Parts 2 and 4** — *finding* the flaws and *repairing* them into an honest, externally-validated workflow are the spine. The corrected workflow should itself pass the checklist it applies.
- **Model the reproducibility lesson:** fix and report a seed, keep everything inside pipelines, make every reported number regenerable.

## The deliverable

A structured **reviewer report** (Part 6) with a justified recommendation, plus the **original-vs-corrected comparison** (Part 5) and a short **reflection** (Part 7). Grade yourself as a real reviewer would: *is the recommendation justified by the evidence you gathered?*

---

# Part 3 — Revision Summary

## The ten things to remember

1. **Trust is the hard part.** The hardest part of ML is not training a model; it's demonstrating it is trustworthy. Most failures happen *after* model development.
2. **Published ≠ validated.** Publication checks novelty, not generalization or utility; biomarker failure follows a funnel from "published" to (rarely) "adopted."
3. **Failures are evidence failures, not algorithm failures** — overfitting, small cohorts, dataset bias, no external validation, publication bias.
4. **Internal validation never leaves the cohort.** Nested CV makes your internal estimate honest; only **external** validation tests generalization.
5. **External = a different cohort, used once.** A held-out split is internal; the internal→external drop is the most informative (and most omitted) number.
6. **Distribution shift** explains the drop — covariate / label / **concept** shift; concept shift can invalidate a biomarker outright.
7. **Survival > binary.** Time-to-event with censoring is the honest outcome; a binary cutoff mislabels late recurrers (critical in late-recurring HR+ disease).
8. **Two reproducibilities.** Computational (rerun → same numbers) vs scientific replication (new data → same finding); passing the first doesn't guarantee the second.
9. **Significance ≠ clinical utility.** Utility needs actionability, incremental value over clinical variables (grade/stage/nodal/Ki-67), and net benefit — not just a small p-value.
10. **Exploratory ≠ confirmatory**, and reviewers check the claim against the evidence (external validation, leakage/order, baselines, reproducibility). New methods raise the ceiling, not lower the evidence bar.

## Key terms

- **Internal vs external validation** — an honest performance estimate *within* the discovery cohort (split, CV, nested CV) vs a test of *generalization* on a genuinely independent cohort (different study/site/population/platform, used once). The first controls overfitting and tuning optimism; only the second tells you the model travels.
- **Distribution / dataset shift (covariate / label / concept)** — the data distribution differs between development and deployment. *Covariate*: feature distributions differ, rule holds (new platform, younger cohort). *Label*: outcome frequency differs (screening cohort with fewer recurrences). *Concept*: the feature→outcome relationship itself changes (new standard-of-care therapy) — the dangerous one, because recalibration can't rescue it.
- **Censoring & survival (time-to-event) analysis** — *censoring* is following a patient without observing an event (lost to follow-up, or event-free at last contact). Survival models (Cox, conceptually) model *when* events occur and handle censoring, rather than collapsing everything to a binary yes/no at one cutoff — the research-grade, more honest framing of the same prediction problem.
- **Computational reproducibility vs scientific replication** — rerun the same code + data → the same numbers (transparency/bookkeeping) vs reproduce the *finding* in new data with a new pipeline (is it real?). You can pass the first and fail the second.
- **Statistical significance vs clinical utility** — significance establishes that an association exists (and is cheap at large n); utility asks whether the result is large enough and actionable enough to change a decision and help a patient.
- **Incremental / added value** — performance over and above the clinical variables already in routine use (grade, stage, nodal status, Ki-67); the comparison most often omitted and most decisive for adoption.
- **Exploratory vs confirmatory** — hypothesis-generating analysis that screens many options (optimistic by nature) vs a pre-specified model and endpoint tested exactly once on untouched data; mislabelling the former as the latter is the field's core methodological sin.
- **The reviewer's six** — no external validation, leakage, weak biological interpretation, circular feature selection, missing baselines, unrealistic performance; every one is an *evidence* failure, and the same list is your pre-submission self-review.
- **Publication bias** — the optimistic results are disproportionately the ones published, so the literature as a whole overstates how well biomarkers work; invisible in any single paper, so calibrate trust to the *field*, not one study.

## Self-test (try before checking back against Part 1)

1. A model scores 0.80 in nested CV on the discovery cohort. A colleague says "nested CV is the gold standard, so it's validated." What's wrong with that, and what would you ask for? *(§3–4)*
2. Why is testing on a different *platform* (Illumina → Affymetrix) more honest than a random held-out split? *(§4)*
3. You re-tune your signature on three external cohorts and report the best. What have you actually done? *(§5)*
4. A new adjuvant therapy changes who recurs. Which kind of shift is that, and why can't recalibration fix it? *(§7)*
5. Two patients are both "non-recurrers" at a 5-year binary cutoff — one recurred at 5.5y, one was censored at 2y. How does a survival framing treat them differently, and why does it matter in HR+ disease? *(§8)*
6. A paper shares all code and data and you regenerate every figure exactly. Is the biomarker reproducible? *(§9)*
7. A biomarker is significant at p < 0.0001 in 3,000 patients but separates 10-year risk by 2%. Is it clinically useful? What's the comparison it most needs? *(§11–12)*
8. You review "a novel 50-gene signature, cross-validated AUC 0.92." List the four questions you'd ask first and the red flag behind each. *(§14–15)*

## One sentence to leave with

> **The hardest part of machine learning is not training a model — it is demonstrating that the model is trustworthy;** the judgment to demand validation, distrust a number until it earns trust, and ask whether it helps a patient is the skill that outlasts every algorithm.
