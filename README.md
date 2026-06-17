# ml26 — Practical Machine Learning for Transcriptomics in Cancer Research

**From Biological Question to Research-Grade Predictive Model.**

A course for PhD students in computational biology, bioinformatics and translational oncology.
It is organised as **5 sections** built around a single running case study — predicting risk of
recurrence in HR+/HER2− early breast cancer from tumour microarray transcriptomics (METABRIC,
with an external GEO cohort for genuine batch effects).

The full syllabus is in [`lessons/overview.md`](lessons/overview.md).

---

## Repository Structure

```
ml26/
├── README.md
├── STRUCTURE.md
├── .gitignore
├── lessons/
│   ├── overview.md                       # Full course syllabus (5 sections)
│   ├── lesson01_biological_question/      # Section 1 — framing the ML problem
│   │   ├── theory/
│   │   │   ├── slides/                    # Slide decks (.pptx)
│   │   │   └── notes/                     # Student notes (Lecture N - Student Pack.md)
│   │   └── practical/
│   │       └── task/                      # Student-facing exercises
│   │           ├── README.md
│   │           ├── datasets/
│   │           └── notebooks/
│   ├── lesson02_baseline_model/           # Section 2 — first predictive model
│   ├── lesson03_feature_engineering/      # Section 3 — biological representation
│   ├── lesson04_model_validation/         # Section 4 — improving & validating
│   └── lesson05_research_grade_ml/        # Section 5 — research-grade ML
└── solutions/                            # Reference solutions (mirrors lessons/)
    ├── README.md
    └── lessonNN_<topic>/
        ├── notebooks/                     # lessonNN_solutions.ipynb
        └── reports/                       # example deliverable
```

---

## Lesson ⇄ Section Mapping

| Lesson | Section | Topic | Deliverable |
|---|---|---|---|
| `lesson01_biological_question` | 1 | From Biological Question to ML Problem | Project specification |
| `lesson02_baseline_model` | 2 | Building the First Predictive Model | Baseline model report |
| `lesson03_feature_engineering` | 3 | Feature Engineering & Biological Representation | Feature engineering notebook |
| `lesson04_model_validation` | 4 | Improving and Validating Models | Model comparison report |
| `lesson05_research_grade_ml` | 5 | Research-Grade Machine Learning | Research-style report + pipeline |

---

## Naming Conventions

| Item | Convention | Example |
|---|---|---|
| Lesson folder | `lessonNN_<snake_case_topic>` | `lesson03_feature_engineering` |
| Exercise notebook | `lessonNN_exercises.ipynb` | `lesson01_exercises.ipynb` |
| Solution notebook | `lessonNN_solutions.ipynb` | `lesson01_solutions.ipynb` |
| Student notes | `Lecture N - Student Pack.md` | `Lecture2 - Student Pack.md` |
| Slide deck | `Lecture N - <title>.pptx` | `Lecture2 - Building the First Predictive Model.pptx` |

- `NN` is a zero-padded two-digit number (01, 02, …) for folder/notebook names.
- Use **lowercase snake_case** for folder and notebook names; lecture-facing
  decks and student packs keep their human-readable `Lecture N - …` titles.

---

## Adding a New Lesson

```bash
LESSON=lesson06_<topic>
mkdir -p lessons/${LESSON}/theory/{slides,notes}
mkdir -p lessons/${LESSON}/practical/task/{notebooks,datasets}
mkdir -p solutions/${LESSON}/{notebooks,reports}
```

Then add a `practical/task/README.md`, the student notes (`Lecture N - Student Pack.md`)
in `theory/notes/`, and the deck (`Lecture N - <title>.pptx`) in `theory/slides/`. Commit:

```bash
git add lessons/${LESSON} solutions/${LESSON}
git commit -m "${LESSON}: add theory and exercises"
```

---

## Environment Setup

```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt  # add this file when dependencies are defined
jupyter notebook
```

---

## License

Course materials © the authors. All rights reserved unless stated otherwise.
