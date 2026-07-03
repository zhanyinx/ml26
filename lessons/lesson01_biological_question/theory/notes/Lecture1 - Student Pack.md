# Lecture 1 — Student Pack
## From Biological Question to Machine Learning Problem

*Practical Machine Learning for Transcriptomics in Cancer Research*

These notes accompany Lecture 1 and the first practical. They are meant to be read **after** the lecture as a study aid, and **before** the practical as a prep guide. Nothing here replaces attending — the discussion is where the ideas stick — but everything you need to revise and to get started is in this one document.

This pack has three parts:

1. **Lecture handout** — the ideas, in prose, in the order they were taught.
2. **Practical prep & setup guide** — what to install, what data we use, and what you'll do.
3. **Revision summary** — a one-page-style condensation plus self-test questions.

A note on the running theme: the entire course follows one real clinical problem — **predicting risk of recurrence in HR+/HER2− early breast cancer from tumour transcriptomics**. Lecture 1 contains no algorithms on purpose. The bet is that biologists learn ML faster by first mastering *how to frame the problem and how data fails* than by jumping to models. The single sentence to carry out of today:

> **Building a trustworthy predictive biomarker is more important than choosing a sophisticated algorithm.**

---

# Part 1 — Lecture Handout

## 1. The clinical setting: why this problem matters

HR+/HER2− breast cancer is the most common subtype, roughly 70% of cases. Prognosis is generally good, but the group is heterogeneous: some tumours genuinely need chemotherapy, and many do not. That creates a two-sided clinical danger:

- **Overtreatment** — giving chemotherapy to patients who would have done well anyway. The cost is real toxicity, expense, and harm.
- **Undertreatment** — withholding therapy from patients who go on to relapse. The cost is missed recurrences.

The unmet need is a tool that tells us *who actually benefits* from added therapy. Crucially, we cannot see this split with the naked eye — two tumours that look identical clinically can behave completely differently. That is the gap a transcriptomic biomarker tries to fill.

## 2. A biological question is not yet an ML problem

A clinician asks: *"Will this patient's tumour respond to treatment?"* That sentence is **underspecified** — it is not yet something a computer can be trained to predict. To turn it into a machine-learning problem we must commit to four things:

- **What we measure** (the input)
- **What we predict** (the output / label)
- **When** we measure it
- **On whom**

The key insight, and one you should internalise early: **most of the scientific judgement — and most of the danger — lives in this translation step, not in the model.** A brilliant algorithm fed a badly-framed question produces confident nonsense.

## 3. Defining the label is a scientific choice

"Response" is not one thing. Candidate endpoints include:

| Endpoint | When available | Caveat in HR+/HER2− |
|---|---|---|
| pathologic complete response (pCR) | early, at surgery after neoadjuvant therapy | rare in luminal disease; a *weak* surrogate for long-term survival |
| Residual Cancer Burden (RCB) class | early, at surgery | graded, but the same surrogacy concern |
| relapse / distant metastasis-free survival (DMFS) | needs years of follow-up | closest to what patients actually care about |

The label is **a scientific choice with biological consequences**, not a given. pCR is convenient because it is measured early and is binary — but in luminal (HR+/HER2−) disease pCR is uncommon and correlates only weakly with survival. A model that predicts pCR well in this setting may be predicting something of limited clinical value.

**What our course actually does.** The cohort we use (METABRIC, introduced next) is *not* a neoadjuvant trial and has **no pCR** at all. It has long-term relapse/survival follow-up. So our practical predicts **recurrence** — a relapse/DMFS-style endpoint — which is also the endpoint family used by the published HR+/HER2− model this course follows. The lesson is not "pCR bad, relapse good." It is: **let the available, meaningful data choose your endpoint, and document that choice.**

**A simplification we make on purpose — keep this in mind.** Relapse is naturally *time-to-event*: it records both *whether* a patient relapsed and *when*. To keep the modelling simple while you learn the workflow, we collapse it into a **binary label** (relapsed vs not, by a fixed time horizon). That throws information away:

- a relapse at 11 months and one at 110 months get the *same* label, even though their biology and prognosis differ enormously;
- patients **censored** before the horizon (event-free but with short follow-up) don't fit cleanly into a binary scheme and usually get dropped, which shrinks and biases the cohort.

We are choosing this deliberately and will return to its cost in Section 5, where the research-grade model keeps the time dimension. For now: just know that "binary" was a *decision*, not a default.

## 4. Where the data come from

The path from tumour to data is a chain of human and instrument decisions, every one of which can inject artefacts:

> tumour biopsy → RNA extraction → microarray hybridisation → scanned intensities → normalised expression matrix

Each arrow carries a choice or an artefact source: how much of the biopsy is tumour vs stroma, RNA degradation, reagent lot, scanner, normalisation method. Microarrays (Affymetrix, Illumina) carry probes for tens of thousands of transcripts, where intensity is proportional to abundance. Many landmark, fully-annotated public cohorts are microarray-based, which is why this course uses them even though RNA-seq is now more common.

**Our datasets.** We use **METABRIC** (~1,900 tumours, Illumina HT-12 platform; Pereira et al. 2016, accessed via cBioPortal) as the primary cohort — it is the development cohort of the recurrence model we follow. For the practical we deliberately **combine it with a second GEO cohort, GSE6532 (Loi et al.)**, which used a *different* array platform (Affymetrix). Two cohorts on two platforms is exactly where a **batch effect** is born — and that is the point: it gives you a real one to discover.

## 5. The transcriptomic data matrix

The central object of the whole course is an **expression matrix**. Conventionally for ML:

- **rows = samples** (patients/tumours)
- **columns = genes/probes**

…but bioinformatics tools often store the transpose (genes as rows), so **always state your convention explicitly** — transposition errors are a real and embarrassing source of bugs. A typical study is ~100–500 samples × ~20,000 genes. Picture it as a wide, short rectangle. That shape is the source of nearly every difficulty in the course.

- One **row** = patient 17: everything we measured about that tumour.
- One **column** = gene X across all patients.

## 6. Features and labels, concretely

- **Features (X):** the expression value of each gene for each sample — the wide matrix.
- **Label (y):** the chosen recurrence endpoint per sample — the one column we are trying to predict.
- **Optional extra features:** clinical covariates (age, tumour size, grade, nodal status).

Mental model: *for each patient, ~20,000 numbers describing the tumour, and one number we wish we knew in advance.*

## 7. Why this is hard: p ≫ n

We have far more features (p ≈ 20,000) than samples (n ≈ hundreds to low thousands). This is the **defining property of transcriptomic ML: p ≫ n**, and it holds even for a large cohort like METABRIC (~1,400 HR+/HER2− tumours is still vastly fewer than 20,000 genes). Contrast this with classic ML, where you usually have far more samples than features (n ≫ p). Omics data are "short and fat," and short-fat data behave counterintuitively.

## 8. The curse of dimensionality (intuition)

In very high-dimensional space, almost everything looks far from everything else; the space is essentially empty. With 20,000 dimensions and 200 points, there is so much room that you can **always find some combination of genes that perfectly separates your patients — even if the labels were assigned at random.** Separation, by itself, is therefore *not evidence of a real signal*. This is why, in this regime, **overfitting is the default, not the exception.**

## 9. Overfitting in the p ≫ n regime

Overfitting means the model **memorises the specific patients rather than learning the underlying biology**. With more knobs than data points it is trivially easy. The signature symptom is **near-perfect performance on the data you trained on and collapse on new data**. A memorable framing: *with enough genes, you can fit the noise in your sample as confidently as the signal.*

## 10. Signatures are not unique

Reanalyses of famous breast-cancer signatures found that many **different, barely-overlapping gene sets predict outcome about equally well**. Published signatures are often **unstable**: resample the patients and a different gene set gets selected. The implication is uncomfortable but important — **a gene appearing in your signature is weak evidence that it is biologically causal.** This is a structural feature of p ≫ n, not necessarily a flaw in any single study. Two papers with non-overlapping signatures are not automatically contradicting each other.

## 11. Batch effects: the silent confounder

A **batch effect** is systematic technical variation tied to *when, where, and how* samples were processed — run date, machine, technician, reagent lot, hospital, platform. Two things make it dangerous:

1. it can be **larger than the biological signal** you care about; and
2. if it is **correlated with the outcome** (e.g. most relapses happened to be processed at one site or on one platform), the model can score beautifully by **learning the batch instead of the biology.**

## 12. Seeing batch effects with PCA

**PCA** (principal component analysis) projects the high-dimensional matrix onto a few axes that capture the most variance, letting you eyeball the dominant structure. The teaching move is to plot the *same* PCA and **colour it two ways**:

- colour by **batch/platform** → if the points separate cleanly (e.g. along PC1), batch dominates the variance;
- colour by the **biological label** → if the points are thoroughly mixed, the dominant axis is *not* your biology.

In our combined METABRIC + GSE6532 data, PCA on the top ~2,000 most variable shared genes shows PC1 separating the two platforms cleanly, while the recurrence label — available only on the METABRIC samples — shows no comparable structure; with outcome observed on just one platform you cannot even assess mixing across the divide — *"the array knew the platform before it knew the tumour."* Two cautions: **PCA is a diagnostic, not a fix** (correction methods like ComBat come later and have their own pitfalls), and PCA *reveals* structure without telling you its cause.

## 13. Data leakage: the most expensive mistake

**Leakage** is when information from outside the training set sneaks into model building. The result is a model that looks brilliant in development and fails in the real world — it is the single most common reason "great" biomarkers do not replicate. Today we learn to **recognise** it; later in the course we learn to **prevent** it systematically. Four flavours specific to transcriptomics:

- **Feature-selection leakage** — picking the "best" genes using *all* samples *before* splitting into train/test. The classic killer.
- **Normalisation leakage** — computing scaling statistics (mean, variance, quantiles) over train + test together.
- **Sample leakage** — technical replicates, multiple biopsies from one patient, or related patients split across train and test.
- **Temporal / label leakage** — using information that only became available *after* the outcome was known.

## 14. When a biomarker collapses: Duke / Potti

A set of chemosensitivity gene signatures were published in top journals and were heading into clinical trials before independent forensic reanalysis (Baggerly & Coombes) could not reproduce them; data errors surfaced, trials were suspended, papers were retracted. The lesson is **not** "those scientists were bad people." It is that **the pipeline was undocumented and the failure modes we are discussing were not controlled.** Rigorous process is what protects you. 

## 15. Why biomarkers fail validation — the pattern

The recurring story is **spectacular discovery, silent validation failure**: discovery AUC ~0.95 dropping to ~0.6 in an independent cohort. The usual culprits:

- optimistic bias from leakage and tuning on the test set;
- tiny, underpowered cohorts in p ≫ n;
- batch confounding mistaken for biology;
- overfitting to a single dataset, with no independent cohort;
- selective reporting of the best-looking result.

## 16. What success looks like

Biomarkers that *did* hold up (e.g. the multi-gene recurrence assays used clinically in HR+ breast cancer) share three traits:

- a **pre-specified, locked-down** signature;
- **independent, multi-cohort** validation;
- demonstrated **clinical utility**, not just a statistical association.

The takeaway: rigour in framing and validation — not algorithmic novelty — is what made them trustworthy.

## 17. The defensible workflow

The order of operations is itself a scientific commitment:

1. **Lock** the question and the label.
2. **Split** the data *first*.
3. Everything data-dependent — normalisation, feature selection, tuning — happens **inside the training data only**.
4. **Touch the test set once**, at the very end.

The slogan: **"Split first, decide everything else downstream of the split."** Picture a wall dropped in right after the split; the test set sits behind it, opened exactly once.

## 18. Train / validation / test

- **Training set** — used to fit the model.
- **Validation set** — used to tune choices (which genes, which settings); used *many* times.
- **Test set** — used to estimate real-world performance; used **once**, never for decisions.

In small cohorts, **cross-validation** replaces a single fixed validation set (we'll cover this properly later — for now it's just a preview).

## 19. Splitting pitfalls specific to omics

- **Split by patient, not by sample** — keep all of one patient's material on one side of the wall. (Two biopsies from the same patient on opposite sides is sample leakage.)
- **Respect batch** — don't let a split accidentally line batches up with the outcome.
- **Preserve class balance (stratify)** — relapses may be a minority, and a careless split can leave a fold with almost none.
- The **gold standard** is an independent **external cohort**, ideally from another institution and/or platform.

## 20. The main message (again, because it matters)

> "Building a trustworthy predictive biomarker is more important than choosing a sophisticated algorithm."

A simple model on a clean, leak-free, well-validated pipeline beats a fancy model on a compromised one — every time. The hard part is the science *around* the model: the question, the label, the data structure, the splits, the validation.

---

# Part 2 — Practical Prep & Setup Guide

> **Goal of the practical:** *know your data before you trust it.* There is **no modelling** in this session. You will load a real transcriptomic dataset, audit its clinical metadata, define a clean label, measure class balance, run PCA, discover a batch effect, and build a defensible train/validation/test split. The mindset to adopt is that of an analyst writing the "data sanity" section of a methods paper — not someone racing to a result.

## What you'll need

- **Python** with Jupyter (a recent Anaconda install is fine).
- Core libraries: `numpy`, `pandas`, `scikit-learn`, `matplotlib`, `seaborn`.
- Comfort with `pandas` DataFrames (indexing, joins, value counts) is assumed; you do **not** need prior ML experience.

*(Your instructor will tell you whether the data is pre-downloaded for you or whether you retrieve it in-session. Don't spend prep time wrestling with downloads — note any access questions and bring them.)*

## The dataset (so you arrive oriented)

- **Primary cohort: METABRIC** — ~1,900 breast tumours, Illumina HT-12 microarray, with rich clinical annotation and **long-term survival follow-up**. Accessed via cBioPortal (`brca_metabric`; Pereira et al. 2016). We filter to the **HR+/HER2−** subtype.
- **Second cohort: GSE6532 (Loi et al.)** — a GEO series on a *different* (Affymetrix) platform, combined in **deliberately** so a genuine cross-platform batch effect exists for you to find.
- **Label:** a **binary recurrence** endpoint derived from the survival fields (relapse within a fixed horizon). METABRIC has **no pCR** — that's expected, and is itself a teaching point.
- **Batch covariate:** simply the **source cohort / platform** (METABRIC/Illumina vs GSE6532/Affymetrix).

## What you'll do, section by section

**0. Setup & framing (~10 min).** Write, in your own words, the prediction task: input, output, when measured, on whom. *Output: a short written task statement.*

**1. Loading & orienting (~20 min).** Load the expression matrix and the clinical table. Work out — and **assert** — which axis is samples and which is genes (use counts, value ranges, join keys; don't guess). Join expression to metadata and check how many samples matched. *Output: confirmed matrix shape, a clean joined table, a note on any unmatched samples.*
→ *Think about:* what real-world events make samples fail a join, and why silently dropping them is dangerous.

**2. Auditing metadata & defining the label (~25 min).** Find the recurrence/survival fields. Build a clean **binary** label (relapse within your chosen horizon), and **record every decision**: the horizon, how you handle **censored** patients and short follow-up, how you treat deaths without relapse. Identify at least one field that must **not** be used as a feature because it would leak (anything measured at/after relapse or last follow-up). *Output: a clean label vector aligned to the matrix, plus a short "label decisions" log including your censoring policy.*
→ *Watch out for:* treating a raw clinical column as objective truth, and assuming a censored patient is a non-event.

**3. Class balance (~15 min).** Count and visualise relapse vs no-relapse; state the minority (event) count. *Output: a class-balance figure and a written interpretation.*
→ *Think about:* if events are ~15–20%, what does a model that always predicts "no relapse" score on accuracy — and why is that a trap?

**4. PCA & batch effects (~40 min — a core section).** Apply light, clearly-flagged *exploratory* preprocessing (not a modelling step), run PCA, and plot PC1 vs PC2 **twice**: once coloured by the recurrence label, once by batch/platform. Decide whether a batch effect is present and whether it is **confounded** with outcome (cross-tabulate batch against label). *Output: the two-colouring PCA plots and a written verdict with evidence.*
→ *Reason about:* if batch and outcome were *perfectly* confounded, why could no correction method rescue the analysis?

**5. Designing the splits (~40 min — a core section).** First build a **naive random split** and measure how the minority class and the batches actually landed — compare against your Section 3 prediction. Then build a proper **patient-level, stratified, batch-aware** split and verify integrity (no patient on two sides; class proportions preserved; batch distribution checked across splits). Finally, the **leakage hunt**: state exactly where normalisation and feature selection must happen (after, and inside, the split) and point to the line in the naive workflow where leakage would have occurred. *Output: split index sets, an integrity-check table, and a short justification.*
→ *Note:* METABRIC and GSE6532 are essentially one-sample-per-patient, so true duplication isn't present here — implement and verify patient-level grouping anyway, as a discipline. Because the labelled cohort here is single-platform (METABRIC only), the **live constraint is stratification of the minority class**; patient-level grouping and batch-awareness are habits to carry forward, not the binding constraint in this dataset.

**6. Reflection memo (~15 min — the main assessable artefact).** Write a 200–300 word **"data readiness" memo**: is this dataset fit for building a trustworthy biomarker? What are its risks (sample size, imbalance, batch confounding, label ambiguity)? What would you fix or request before modelling? This is your honest analyst's verdict.

## How to approach it

- Document every decision as you go — the log *is* the deliverable, not an afterthought.
- The leakage hunt in Section 5 is the payoff: try to *find* the leak yourself before looking anything up.
- If you run short on time, protect Sections 4 and 5 (PCA/batch and splitting) — compress Section 0 and the memo instead.

---

# Part 3 — Revision Summary

## The ten things to remember

1. **Framing beats algorithms.** Turning a vague clinical question into a well-posed prediction task (input / output / when / whom) is where the science and the danger live.
2. **The label is a choice.** Endpoints differ in meaning; pCR is a weak surrogate in luminal disease; we use a **binary recurrence** label here because that's what METABRIC supports — a deliberate, documented decision (and a simplification of time-to-event data).
3. **The data object is a wide, short matrix:** samples × genes, ~hundreds × ~20,000. State your row/column convention explicitly.
4. **p ≫ n** is *the* defining property of transcriptomic ML.
5. **Curse of dimensionality:** in near-empty high-dimensional space you can always separate the classes — even pure noise — so separation is not evidence.
6. **Overfitting is the default** in p ≫ n: great on training data, collapses on new data.
7. **Signatures are non-unique and unstable;** signature membership is weak evidence of causal biology.
8. **Batch effects** are technical variation by when/where/how; dangerous when larger than the signal or correlated with outcome. **PCA diagnoses, it does not fix.**
9. **Data leakage** (feature-selection, normalisation, sample, temporal/label) is the top reason biomarkers fail to replicate.
10. **Defensible workflow:** *split first, decide everything downstream of the split;* train / validation / test; split by patient, respect batch, stratify; external validation is the gold standard.

## Key terms

- **p ≫ n** — far more features than samples (omics regime).
- **Batch effect** — systematic technical variation tied to processing.
- **Confounding** — when a technical variable (batch) tracks the outcome, so the two can't be separated.
- **Censoring** — a patient is event-free but with follow-up too short to know their final outcome.
- **Leakage** — information from outside the training set influencing model building.
- **Stratification** — splitting so class proportions are preserved across train/val/test.
- **PCA** — projection onto top-variance axes; an exploratory diagnostic.

## Self-test (try before checking back against Part 1)

1. A 25-gene classifier perfectly separates responders from non-responders in an 80-patient cohort. What is your *first* reaction, and why? *(§8)*
2. A PCA coloured by processing site separates cleanly along PC1. What does this indicate, and is the dataset ready for modelling? *(§11–12)*
3. A student scales every gene across the whole dataset, ranks genes by association with outcome, keeps the top 50, *then* splits into train/test. Identify every leakage point and fix the pipeline. *(§13, §17, §19)*
4. Two papers report response-predicting signatures that share almost no genes. Does at least one have to be wrong? *(§10)*
5. Why might a patient's core biopsy and surgical specimen need to stay on the *same* side of a split? *(§19)*
6. Why does collapsing a time-to-event outcome into a binary label throw away information — and name one practical cost. *(§3)*

## One sentence to leave with

> **Building a trustworthy predictive biomarker is more important than choosing a sophisticated algorithm** — the rigour around the model is the work.

---

*End of Lecture 1 student pack. Bring your Section 0 task statement and any data-access questions to the practical.*
