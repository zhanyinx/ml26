# Lecture 3 — Student Pack
## Feature Engineering and Biological Representation

*Practical Machine Learning for Transcriptomics in Cancer Research*

These notes accompany Lecture 3 and the third practical. Read them **after** the lecture as a study aid, and **before** the practical as a prep guide. They don't replace attending — the discussion and the live leakage demo are where the ideas stick — but everything you need to revise and to get started is here.

This pack has three parts:

1. **Lecture handout** — the ideas, in prose, in the order they were taught.
2. **Practical prep & setup guide** — what you'll need, the data, and what you'll do.
3. **Revision summary** — a condensed recap plus self-test questions.

A reminder on the running theme: the whole course follows one real clinical problem — **predicting risk of recurrence in HR+/HER2− early breast cancer from tumour transcriptomics**. Lecture 1 framed the problem and the data; Lecture 2 built a trustworthy baseline by changing the *model*. This lecture is the pivot: we hold the model fixed and change the *features* — the representation of the biology. It is, arguably, the most important lecture in the course, because this is where your training as a biologist becomes a modelling advantage. The single sentence to carry out of today:

> **Better biological representation often improves predictive performance more than changing algorithms.**

---

# Part 1 — Lecture Handout

## 1. The big idea: representation over algorithm

A model never sees "biology." It sees a matrix of numbers we chose. Change those numbers — change the **representation** — and you change everything, often more than swapping Logistic Regression for a neural network would. Picture a fork: the arrow marked "change the algorithm" is thin; the arrow marked "change the representation" is thick. For most of ML, people obsess over the thin arrow; in transcriptomics, the wins are on the thick one. Twenty thousand raw genes and fifty pathway scores can describe the *same* tumours — the second isn't less information in any way that matters; it's the same biology, denoised and organised. The habit to break: *"better performance comes from better algorithms."* In p ≫ n with strong prior biology, representation usually wins.

## 2. Why features matter

A flawless algorithm fed a poor representation still fails, because the discriminating structure isn't *accessible* in the features. The key asymmetry: 20,000 noisy genes carry enormous noise per unit signal; 50 meaningful pathway scores carry far less. Features are also where we **inject prior knowledge** — decades of cancer biology — so the model needn't rediscover it from a few hundred patients it doesn't have the data to. The clinically adopted breast-cancer recurrence assays are, at heart, *feature engineering*: a small curated set of genes (heavy on proliferation and ER signalling), not an exotic algorithm. Drop the instinct that *more features = more information = better*; more features means more **noise** and more ways to overfit, unless they carry signal.

## 3. A concrete comparison (our case study)

Same METABRIC patients, the same Logistic Regression from Lecture 2, and several representations: all genes / filtered genes / pathway scores. This is a **controlled experiment** — only the representation changes, so any gain is attributable to it. The hypothesis we test in the practical: *fewer, biologically-coherent features generalise as well or better.* The honest inverse question — when might raw genes **win**? — when a real signal sits in a single gene no curated set captures, or in a very large cohort where p ≫ n bites less. And remember: detail and noise come together, so the question is never raw detail, it's **signal-to-noise**.

## 4. The curse of dimensionality, revisited

p ≫ n again: a few hundred to a couple of thousand patients, ~20,000 genes. With so many features, some correlate with outcome by chance; models are unstable; overfitting is the default. New framing (vs Lecture 1): **representation is one of the few levers that actually reduces p** — moving from 20,000 genes to 50 pathways pushes p back toward n, where statistics behaves. Recall the Lecture 1 demonstration that random gene panels can "predict" outcome; aggregation is a principled way to stop handing the model that opportunity. Don't expect to "beat the curse with more data" — in oncology you usually can't; representation is the lever you control.

## 5. How dimensionality hurts (intuition)

Each noisy gene is one more lottery ticket for a spurious correlation. In very high dimensions, distances and correlations become unreliable. A model has finite effective capacity given its data, and spends it across thousands of mostly-irrelevant genes; aggregating to meaningful features concentrates the signal and stabilises learning. If 19,950 of 20,000 genes are noise for your outcome, the model spends training mostly **fitting noise** — which is overfitting. And it cannot reliably "just ignore" the irrelevant genes: with few samples it can't tell which are irrelevant — that inability *is* the problem.

## 6. Feature engineering vs feature selection

These get conflated constantly, so keep them sharp:

- **Feature engineering** *creates* new features (combine many genes into a proliferation score). It is *building*.
- **Feature selection** *chooses* among existing features (keep these 100 genes, drop the rest). It is *picking*.

They're complementary — engineer good features, then select among them. Crucially, both are **data-dependent decisions** and so both live under the Lecture 2 split discipline — *with one subtlety* (Part 11): building a score from a **fixed, published** gene list is largely exempt, because the list wasn't chosen using your outcome.

## 7. Gene filtering: variance filtering

Drop genes that barely vary across patients — they can't discriminate anyone. The virtue: it's **unsupervised** (never looks at the label), so it carries little leakage risk and is a safe first pass to shrink 20,000 → a few thousand. The caveat: high variance ≠ biological relevance (a noisy probe can be high-variance and useless). "Low variance" means *uninformative for discrimination in this dataset* — a narrow, safe claim, not a statement about biological importance.

## 8. Gene filtering: differential expression

Keep genes whose expression differs between recurrers and non-recurrers. Powerful — it targets discriminating genes directly — but **supervised**: it uses the outcome label, which makes it a textbook leakage hazard. Run it on the *whole* dataset then cross-validate, and the kept genes already "know" the test patients' outcomes; your AUC inflates. The rule (extending Lecture 2): **DE filtering happens inside each fold, never before.** Ubiquity is not safety — DE is done before splitting in countless papers, which is part of *why* so many signatures don't replicate. It uses the label, so it is squarely inside the split rule.

## 9. Filtering: advantages and limitations

Advantages: fewer features, faster training, less overfitting, easier interpretation. Limitations: variance filtering keeps high-variance noise; DE filtering leaks and is unstable. The deeper, conceptual limitation that motivates the rest of the lecture: **filtering judges genes one at a time**, ignoring that genes act in coordinated programmes (co-regulation, pathways, redundancy). That limitation is the bridge to biological aggregation.

## 10. Biological feature engineering — the core idea

Instead of thousands of independent genes, summarise **coordinated gene programmes** into a few scores. A signature score aggregates many co-moving genes into one number. Three benefits to remember: **(1) noise reduction** (averaging), **(2) encoded prior knowledge**, **(3) interpretability and stability**. This is where being a biologist pays off — you know which genes belong together. Aggregating doesn't "throw away information"; it throws away mostly *noise* and redundancy while the shared signal is preserved or strengthened.

## 11. Proliferation scores

Proliferation (how fast the tumour divides) is among the strongest prognostic signals in HR+/HER2− disease. A proliferation score averages cell-cycle genes (MKI67, AURKA, CCNB1, CCNB2, BUB1, …) into one axis of aggressiveness. It's already clinical: Ki-67 staining and the proliferation *modules* in commercial recurrence assays do exactly this. One robust score can rival or beat hundreds of individual noisy genes. Sharp question (from Lecture 2): if your proliferation score mostly recapitulates Ki-67, what *extra* value does the transcriptome add? — the incremental-value question worth always asking.

## 12. Estrogen / ER signalling scores

HR+ disease *is* ER-driven, so a score from luminal/ER genes (ESR1, FOXA1, GATA3, …) is almost guaranteed informative. Note the **direction**: higher ER signalling generally means *lower* recurrence risk — it's often protective, marking well-differentiated luminal-A-like tumours. Pair it with proliferation and two features already sketch the risk landscape: high proliferation + low ER signalling ≈ aggressive luminal-B-like biology (the high-risk corner). Why is there still useful variation within HR+ patients we selected for being ER-positive? Because ER-*positivity* is a coarse binary; the *score* captures a continuum of luminal differentiation the binary misses.

## 13. Immune and stromal signatures

Widen the lens from tumour cells to the tumour **ecosystem**. Immune signatures (T-cell, cytotoxic, interferon) capture anti-tumour immunity — often favourable. Stromal signatures (fibroblast, ECM, EMT) capture the microenvironment — can mark progression. Single genes miss the ecosystem; programme-level scores capture compartments, and they're **transferable** across cancers. Honest caveat: bulk transcriptomics is a *mixture* of tumour, immune, and stromal cells, so an immune/stromal score partly reflects cell-type *proportions*, not only per-cell activity (a deconvolution problem).

## 14. Why biological features outperform single genes

Four wins from one idea: **(1)** averaging co-regulated genes cancels gene-specific noise while shared signal adds up (better signal-to-noise); **(2)** a score is far more *stable* under resampling than any single gene (answers Lecture 1's non-uniqueness); **(3)** fewer features → lower p → less overfitting; **(4)** the features carry meaning. Which would reappear on a new cohort — a specific 12-gene LASSO panel, or a proliferation score? The score — it's robust aggregated biology, not a fragile point-selection. Note: here stability and accuracy often *align* rather than trade off.

## 15. Pathway-level features: gene sets

A gene set is a curated list of genes sharing a function. Databases: **MSigDB Hallmarks** (~50 deliberately non-redundant programmes — ideal for modelling), plus Reactome, GO, KEGG for finer detail. The move: turn 20,000 genes into ~50–100 interpretable pathway activities — biological feature engineering done systematically. Hallmark sets like "E2F targets," "G2M checkpoint," "estrogen response early/late" map onto the proliferation and ER biology we hand-built. Trade-off: few/curated sets are stable and interpretable; thousands of overlapping sets give finer resolution but reintroduce dimensionality and instability. *More sets ≠ better resolution.*

## 16. From genes to pathway activities (GSVA / ssGSEA, conceptually)

These methods compute, per patient and per gene set, a single number: how *active* that programme is in that sample. Conceptually they rank/aggregate the set's genes within each sample into a pathway-activity score. We use them as a **tool** — the output is a patients × pathways matrix that feeds the same Logistic Regression. The transformation in one line: a 1,000 × 20,000 gene matrix → a 1,000 × 50 pathway matrix, same patients, friendlier shape. We give up single-gene resolution — usually a good trade in p ≫ n. (Honest note: per-sample scoring can have subtle leakage considerations, but with **fixed published** gene sets the label-leakage risk is low.) Pathway scoring isn't a black box — it's a transparent aggregation of known biology.

## 17. Why pathway features are more robust

Connects to every prior theme: far fewer features → friendlier p:n (dimensionality); each feature averages many genes → less noise, more cross-cohort stability (stability); pathway activities are **more comparable across platforms** than raw probes → mitigates Lecture 1's batch problem; and the axes are interpretable. Our two cohorts (METABRIC/Illumina + GSE6532/Affymetrix) often align better in *pathway* space than raw-gene space, because aggregation averages out platform-specific probe quirks. But pathways *mitigate* batch variation — they don't *fix* it, and aren't a substitute for proper validation.

## 18. Feature selection: the three families

- **Filter methods** — rank features independently of the model (variance, correlation, DE). Fast, model-agnostic, but blind to interactions.
- **Embedded methods** — selection happens *during* fitting (LASSO, Elastic Net). Efficient, model-aware.
- **Tree-based importance** — rank by usefulness for splitting, in two flavours: **impurity / Gini importance** (free with the model and fast, but unstable when genes are correlated) and **SHAP values** (a game-theory share of each gene's contribution to each individual prediction — consistent and *signed*, ranked by mean |SHAP|; costlier, but far more trustworthy for explanation).
- **Wrapper methods (Boruta)** — duplicate every gene and shuffle the copies into noise "shadow" features, then keep only genes that *repeatedly* beat the best shadow. An *all-relevant* selector (not a minimal panel), with a noise baseline that adapts to the data; like any supervised selection it must run **inside the fold**.

Match the method to your goal: speed, sparsity, stability, interpretability. Filters judge each feature alone, so they miss **interactions and redundancy** (two genes useless alone but informative together). There's no single "best" method — usually the simplest one that is leak-free and stable.

## 19. LASSO and Elastic Net as selectors (callback)

LASSO → sparse panel; Elastic Net → keeps correlated groups, more stable (Lecture 2). New point: apply embedded selection **on pathway features** and you get a tiny, interpretable, stable model — a handful of meaningful pathways. The synergy, which is the course's method thesis in one line: **good engineering (pathways) + honest selection (Elastic Net) + honest validation = a defensible biomarker.** Selecting among ~50 interpretable pathways is more stable than among 20,000 genes — but the inside-the-fold rule still applies; pathway selection is *not* automatically leakage-free.

## 20. Feature-selection leakage (the critical idea)

The classic killer: select features (genes, pathways, DE hits) using **all** the data, then cross-validate the reduced set. The selected features have already seen the test patients' outcomes → optimistic, unreproducible scores. This is the **single most common silent error in omics ML**, and it passes peer review constantly because the inflated number looks great *and stable*. Numerically: with 20,000 genes and 200 patients, some genes correlate with outcome by chance; select them on all data and your folds are contaminated before any model is trained. When you read "we selected the top 100 genes, then 10-fold CV, AUC 0.92," your first question is: *was selection redone inside each fold, or once on everything?* And note — **cross-validation does not protect you**: CV around a leaky selection step *launders* the leak into a confident wrong number.

## 21. Wrong vs right workflow

- **Wrong:** feature selection → cross-validation (selection done once, on everything).
- **Right:** cross-validation → *inside each fold*: select → train → evaluate (held-out patients never influence selection).

Scope: the rule covers filtering, DE selection, signature *fitting*, and any data-driven engineering. **Important exemption:** scoring pathways with *fixed, pre-published* gene sets is largely exempt — the sets weren't chosen using your labels, so nothing is fit to your outcome. (Building a score from a *published* list → outside the fold is fine. Building one by picking genes correlated with recurrence *in your data* → must be inside the fold.) Not all engineering goes inside the fold — only the data-dependent parts.

## 22. How big is the leakage effect?

Leakage can inflate AUC by a large, study-changing margin — enough to make pure noise look like a great biomarker — and the inflated estimate is **stable**, so it looks trustworthy. Two questions to carry into every paper: was selection inside the CV, and was there a truly independent external cohort? A sobering fact you'll demonstrate in the practical: a flawed pipeline can report an impressive cross-validated AUC even on **randomly permuted labels** (all real signal destroyed) if selection is done on all data first. A high, stable CV AUC does *not* mean the result is solid — stability can be an artefact of the leak.

## 23. Biological interpretability

Pathway-level models are usually far easier to interpret than gene-level: a handful of named axes vs hundreds of gene coefficients. The dream sentence — *"recurrence risk rises with proliferation and falls with ER signalling"* — is something a clinician can engage with; a 300-gene weight vector is not. Interpretability isn't cosmetic: it supports validation (does the biology make sense?), hypothesis generation, and clinical adoption. Honest limit: the interpretation is only as good as the gene sets, which overlap and embed assumptions. For clinical translation, interpretability is often a *requirement*, not a luxury.

## 24. Strengths and limitations, honestly

**Strengths:** less overfitting (friendlier p:n), more stability, cross-platform robustness, interpretability, encoded prior biology. **Limitations:** a wrong/biased gene set can hide signal; aggregation can blur a true single-gene effect (e.g. a single amplified driver); curated knowledge lags new biology. The meta-point: feature engineering is a **hypothesis** about what matters — it can be wrong, and must be validated, not assumed. A scenario where pathways *lose*: a strong single-gene effect no curated set captures, which aggregation dilutes. So not "biological features are always better" — usually better in p ≫ n with strong pathway structure, but validate, don't assume.

> **Better biological representation often improves predictive performance more than changing algorithms** — representation and rigour first, algorithm last.

---

# Part 2 — Practical Prep & Setup Guide

## What you'll need

- **Python 3.9+** with Jupyter, plus `numpy pandas scikit-learn matplotlib`. The notebook reuses the earlier loaders (which auto-install `GEOparse` if needed) and a provided pathway-scoring helper.
- **Internet access** the first time — the practical reuses the **same METABRIC data cache as Lectures 1–2**, so if you ran those practicals the download is already there and is reused.
- The notebook from the repo: `lessons/lesson03_feature_engineering/practical/task/notebooks/lesson03_exercises.ipynb`.

## The dataset (so you arrive oriented)

You continue with the prepared **METABRIC HR+/HER2−** cohort: the samples × genes matrix, the binary **recurrence** label, and the **patient-level, stratified** train/validation/test split from the earlier practicals. The notebook also provides **fixed, pre-published gene lists** (proliferation, ER signalling, immune, stromal) and a small hallmark-like gene-set collection — these are external knowledge, *not* chosen from your outcome, which is why building features from them is largely leakage-exempt. The second cohort **GSE6532** is available as an optional cross-platform check.

## What you'll do, part by part

This practical is one controlled experiment: hold the model fixed, vary the representation, and see which the data prefers — then learn to catch the leakage that fakes good results.

0. **Setup & framing** — load the prepared data; define one reusable `evaluate(representation)` helper used for every part.
1. **Baseline: all genes** — the all-genes row of your comparison table (honest cross-validated ROC-AUC, PR-AUC, feature count).
2. **Variance-filtered genes** — an unsupervised filter; note *why* it's low-leakage.
3. **Differentially-expressed genes** — a supervised filter, done **inside each fold**; you'll also peek at what the quick-and-leaky version would have reported.
4. **Biological signature features** — build proliferation, ER, immune, stromal scores from the provided gene lists; model the handful of scores.
5. **Pathway-level features** — transform genes → ~50 pathway activities with the provided scorer; model them; check stability vs raw genes.
6. **Compare representations** — assemble the headline table (ROC-AUC, PR-AUC, #features, stability, interpretability) and the bar chart.
7. **Leakage exercise** — diagnose a deliberately flawed workflow, *measure* the optimism gap, and watch a leaky pipeline report a non-trivial AUC on **permuted labels**.
8. **Biological interpretation & recommendation** — your written deliverable.

## How to approach it

- **Predict before you run.** Before Part 6, write down which representation you expect to win and why. The point of the comparison is to test the lecture's thesis on *this* dataset — let it surprise you.
- **Watch the leakage part closely.** Part 7 deliberately does the wrong thing so you can see the optimism, then the permuted-label demo so you see leakage manufacture performance from nothing.
- **Don't chase the number.** The best representation isn't only the highest AUC — weigh stability, interpretability, and cross-platform robustness too.

## The deliverable

A **200–300 word representation recommendation** (Part 8): which representation you'd trust for a recurrence biomarker and why, weighing ROC-AUC/PR-AUC against feature count, stability, interpretability, and cross-platform robustness — and what you'd validate next. (Connect back to Lecture 2: does your best representation add value over a cheap clinical variable like tumour grade?)

---

# Part 3 — Revision Summary

## The ten things to remember

1. **Representation over algorithm.** The model sees the numbers we choose; changing them often beats changing the model. In p ≫ n with strong prior biology, representation usually wins.
2. **Features inject prior knowledge.** Clinically adopted assays are essentially feature engineering (proliferation + ER modules), not exotic algorithms. *More features = more noise*, unless they carry signal.
3. **Representation reduces p.** Moving 20,000 genes → 50 pathways pushes p toward n — one of the few real defences against the curse of dimensionality.
4. **Engineering vs selection.** Engineering *creates* features (a score); selection *chooses* features (keep 100 genes). Both are data-dependent — both respect the split (fixed published gene sets excepted).
5. **Gene filtering:** variance (unsupervised → low leakage, but keeps noise) and differential expression (supervised → powerful but a **leakage hazard**; must live inside the fold).
6. **Biological signatures** (proliferation ↑risk, ER signalling ↓risk, immune, stromal) beat single genes via averaging (less noise), stability, lower p, and meaning.
7. **Pathway features:** ~50 hallmark activities from 20,000 genes (GSVA/ssGSEA as a *tool*) → friendlier p:n, more stable, **more comparable across platforms** (helps batch), interpretable.
8. **Feature selection families:** filter / embedded (LASSO, Elastic Net) / tree-based. The synergy: good engineering + honest selection + honest validation = a defensible biomarker.
9. **Feature-selection leakage** is the top silent error in omics ML: select on all data → CV → inflated, *stable*, irreproducible AUC. **CV does not protect you.** Selection goes *inside each fold*.
10. **Interpretability & honesty:** pathway models tell a clinician-readable story, but feature engineering is a *hypothesis* — a wrong gene set can hide signal or blur a single-gene effect; validate, don't assume.

## Key terms

- **Representation** — the specific features we feed the model (raw genes, filtered genes, signatures, pathways).
- **Feature engineering / feature selection** — *creating* new features vs *choosing* among existing ones.
- **Variance filtering** — unsupervised removal of low-variance genes (low leakage risk).
- **Differential expression filtering** — supervised selection of outcome-associated genes (leakage hazard).
- **Signature score** — an aggregate of co-regulated genes (e.g. proliferation, ER signalling) into one feature.
- **Gene set / pathway activity** — a curated gene list (e.g. MSigDB Hallmark) and its per-sample activity score (GSVA/ssGSEA).
- **Feature-selection leakage** — selecting features using data that includes the held-out/test patients; inflates and launders performance.
- **Filter / embedded / tree-based selection** — the three families of feature-selection methods.

## Self-test (try before checking back against Part 1)

1. A colleague says "we'll improve our biomarker by switching to a deep neural network." What's the more likely big lever, and why? *(§1–2)*
2. Why does moving from 20,000 genes to 50 pathway features help in p ≫ n — name two mechanisms. *(§4, §17)*
3. Define feature engineering and feature selection; give one transcriptomic example of each and say how their leakage risks differ. *(§6)*
4. Why is variance filtering low-leakage but differential-expression filtering a leakage hazard? *(§7–8)*
5. Why might the *average* of 30 proliferation genes be a better feature than the single best one? *(§10, §14)*
6. A paper reports: "top 100 prognostic genes selected, then 10-fold CV, AUC 0.93." State the wrong and right workflows and why the wrong one inflates — and why it's *especially* dangerous. *(§20–22)*
7. Building a proliferation score from a *published* gene list vs from genes *you picked because they correlate with recurrence* — which must go inside the fold, and why? *(§6, §21)*
8. Give a scenario where pathway features would *lose* to raw genes. *(§24)*

## One sentence to leave with

> **Better biological representation often improves predictive performance more than changing algorithms** — so put representation and rigour first, and the algorithm last; deciding *what the model should see* is the more biological, and more powerful, question.
