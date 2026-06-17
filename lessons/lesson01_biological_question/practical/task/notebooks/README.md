# Lesson 01 — Practical: *Meeting Your Transcriptomic Dataset*

**Section 1 — From Biological Question to ML Problem.** No modelling. This session is about *knowing
your data before you trust it*: load a real transcriptomic dataset, audit its clinical metadata,
define a clean label, measure class balance, run PCA, discover a batch effect, and build a defensible
train / validation / test split.

## Files

- `notebooks/lesson01_exercises.ipynb` — the exercise notebook (work through this).
- `datasets/` — where data is cached at runtime (git-ignored; starts empty with a `.gitkeep`).
- Reference solution: `solutions/lesson01_biological_question/notebooks/lesson01_solutions.ipynb`
  (repo root).

## Data (downloaded automatically)

| Cohort | Platform | Source |
|---|---|---|
| **METABRIC** (~1,900 tumours) | Illumina HT-12 | cBioPortal `brca_metabric` |
| **GSE6532** (Loi et al.) | Affymetrix | NCBI GEO |

We predict **recurrence** (a binary relapse / DMFS-style endpoint). METABRIC has **no pCR** — that is
expected, and is itself a teaching point about letting the data choose the endpoint. The two cohorts
are combined **deliberately** so a real cross-platform batch effect exists for you to find.

## How to run

1. **Requirements:** Python 3.9+, Jupyter, and `numpy pandas scikit-learn matplotlib requests`.
   The notebook auto-installs `GEOparse` (for the GEO download) on first run.
2. **Open the notebook** from this folder:
   ```bash
   cd lessons/lesson01_biological_question/practical/task
   jupyter lab notebooks/lesson01_exercises.ipynb
   ```
3. **Run the setup cell.** It resolves the `datasets/` cache folder automatically and prints where
   data will be stored.
4. **Run the loader cells.** First run downloads METABRIC (~50 MB) and GSE6532 into `datasets/`
   and caches them; subsequent runs reuse the cache. **Internet access is required.**

> **Note on data & git.** Downloaded data is **not committed** — `datasets/` is git-ignored (kept via
> `.gitkeep`). Anyone who clones the repo re-fetches the data by running the notebook. Check the
> current cBioPortal / GEO access terms before redistributing any pre-downloaded copy.

## Deliverable — Project specification

The assessable artefact for this section is a **data-readiness memo** (200–300 words, Section 6 of the
notebook): is this dataset fit for building a *trustworthy* recurrence biomarker? Cover sample size,
class imbalance, batch confounding across the two platforms, and label/censoring ambiguity, and say
what you would fix or request before any modelling.

### Checklist

- [ ] Written prediction-task statement (Section 0)
- [ ] Confirmed matrix orientation + clean joined table (Section 1)
- [ ] Clean binary label vector + label-decisions log incl. censoring policy (Section 2)
- [ ] Class-balance figure + interpretation (Section 3)
- [ ] Two-colouring PCA plots + batch/confounding verdict (Section 4)
- [ ] Patient-level, stratified, batch-aware split + integrity-check table (Section 5)
- [ ] Naive-vs-proper split comparison + identified leakage point (Section 5)
- [ ] **Final data-readiness memo (Section 6)**

> Course message: *building a trustworthy predictive biomarker is more important than choosing a
> sophisticated algorithm.* Everything here is that rigour — before a single model is trained.
