# Practical Machine Learning for Transcriptomics in Cancer Research

**From a biological question to a research-grade predictive model.**

Welcome! This is a hands-on course for PhD students in computational biology, bioinformatics,
and translational oncology. Over **five lessons** you will build a complete, trustworthy
machine-learning pipeline around one real clinical problem — and learn to spot the methodological
mistakes that sink most biomarker studies.

> The central message of the course:
> **building a trustworthy predictive biomarker matters more than choosing a sophisticated algorithm.**

You do **not** need prior machine-learning experience. Comfort with Python and `pandas`
(indexing, joins, value counts) is assumed.

---

## The running case study

Every lesson works on the same problem:

**Predicting risk of recurrence in HR+/HER2− early breast cancer from tumour transcriptomics.**

You train on the **METABRIC** cohort (Pereira et al. 2016, via cBioPortal) and meet a second
cohort, **GSE6532** (Loi et al., on a different array platform), so you encounter a genuine
batch effect. The prediction target is a **binary recurrence** label derived from long-term
follow-up — a deliberate simplification whose cost is examined honestly in Lesson 5.

The full syllabus is in [`lessons/overview.md`](lessons/overview.md).

---

## The five lessons

| # | Lesson | What you build | Deliverable |
|---|---|---|---|
| 1 | [From Biological Question to ML Problem](lessons/lesson01_biological_question/) | Problem framing, data audit, leakage-aware splits | Data-readiness memo / project spec |
| 2 | [Building the First Predictive Model](lessons/lesson02_baseline_model/) | A validated, regularised baseline | Baseline model report |
| 3 | [Feature Engineering & Biological Representation](lessons/lesson03_feature_engineering/) | Signatures & pathway features vs raw genes | Feature-engineering recommendation |
| 4 | [Improving & Validating Models](lessons/lesson04_model_validation/) | Model families, nested CV, batch correction | Model comparison report |
| 5 | [Research-Grade Machine Learning](lessons/lesson05_research_grade_ml/) | Reviewer simulation + corrected pipeline | Reviewer report + reproducible notebook |

Each lesson folder contains:

- **`theory/`** — your lecture **Student Pack** (`theory/notes/Lecture N - Student Pack.md`) and the
  slide deck (`theory/slides/*.pptx`). Read the Student Pack after the lecture and before the practical.
- **`practical/task/`** — the practical: a `README.md` brief, the exercise notebook in
  `notebooks/`, and a `datasets/` cache folder (populated automatically on first run).

---

## Setup

Everything starts with **Lesson 0** — it creates the environment and downloads the data, once.

**1. Create the `ml26` conda environment.** You need **conda**; if you don't have it, install
**Miniconda** first (~5 min): <https://docs.anaconda.com/miniconda/>. Then, from the repository root:

```bash
conda create -n ml26 python=3.11 -y
conda activate ml26
pip install -r requirements.txt
python -m ipykernel install --user --name ml26 --display-name "Python (ml26)"
jupyter lab
```

The `ipykernel` step registers the environment as a Jupyter kernel named **`ml26`** — every notebook
in the course is set to that kernel, so pick **Python (ml26)** in Jupyter's kernel picker.
(One optional package, `lifelines`, is used by a single Lesson 5 cell and is skipped if missing.)

**2. Run Lesson 0 once** to download the data:
[`lessons/lesson00_prerequisites/lesson00_prerequisites.ipynb`](lessons/lesson00_prerequisites/).
See [`lessons/lesson00_prerequisites/README.md`](lessons/lesson00_prerequisites/README.md).

### A note on the data and the lesson-to-lesson hand-off

Lesson 0 **downloads the data once** (internet required): **METABRIC** from the cBioPortal datahub
(expression matrix **~660 MB**) and **GSE6532** from NCBI GEO (**~180 MB**). It is cached under
`lessons/lesson01_.../datasets/` and shared across all lessons.

From there the lessons **hand data forward with checkpoints**: **Lesson 1** reveals the loader
functions and saves a prepared-cohort checkpoint; **Lessons 2–5 load it** (Lesson 3 in turn saves an
engineered-features checkpoint for Lessons 4–5). If a lesson's checkpoint is missing it tells you
which earlier lesson to run first. All caches and checkpoints (`datasets/`, `datasets/derived/`) are
**git-ignored** — never commit them.

---

## How to work through a practical

1. Read the lesson's **Student Pack** and the `practical/task/README.md`.
2. Open the exercise notebook (`practical/task/notebooks/lessonNN_exercises.ipynb`) and work through
   its sections in order — each ends in a short written reflection or a figure.
3. Produce the lesson's **deliverable** (a short report or memo; see each README).
4. Save your completed notebook as `lessonNN_exercises_<your_name>.ipynb` and submit per your
   instructor's directions.

Assessment focuses on **correct methodology, validation strategy, biological interpretation, and
reproducibility** — not on squeezing out the highest accuracy.

---

## Reference solutions

Worked solutions for every lesson live in [`solutions/`](solutions/), mirroring the lesson layout
(`solutions/lessonNN_*/notebooks/lessonNN_solutions.ipynb`). Try the exercises yourself first — the
learning is in the struggle, not the answer key.

---

## For instructors / contributors

Repository layout, naming conventions, and how to add a lesson are documented in
[`STRUCTURE.md`](STRUCTURE.md).

## License

Course materials © the authors. All rights reserved unless stated otherwise.
