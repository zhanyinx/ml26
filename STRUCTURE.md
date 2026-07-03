# Repository Structure: `ml26`

**Practical Machine Learning for Transcriptomics in Cancer Research** — a 5-section course built
around a single running case study (HR+/HER2− breast-cancer recurrence prediction from METABRIC
transcriptomics). Each lesson maps to one course section and splits into **theory** (student
notes + slides) and **practical** (task notebooks + datasets). Reference solutions live in a
top-level `solutions/` tree that mirrors `lessons/`.

```
ml26/
├── README.md
├── STRUCTURE.md
├── .gitignore
├── lessons/
│   ├── overview.md                       # Full 5-section syllabus
│   ├── lesson00_prerequisites/            # One-time setup: conda env `ml26` + data download
│   │   ├── README.md
│   │   ├── requirements.txt
│   │   └── lesson00_prerequisites.ipynb  # Installs env + downloads data into the shared cache
│   ├── lesson01_biological_question/      # Section 1
│   │   ├── theory/
│   │   │   ├── notes/
│   │   │   │   └── Lecture1 - Student Pack.md
│   │   │   └── slides/
│   │   │       └── Lecture1 - From Biological Question to ML Problem.pptx
│   │   └── practical/task/
│   │       ├── README.md
│   │       ├── notebooks/lesson01_exercises.ipynb
│   │       └── datasets/
│   ├── lesson02_baseline_model/           # Section 2
│   │   ├── theory/{notes/Lecture2 - Student Pack.md, slides/*.pptx}
│   │   └── practical/task/{README.md, notebooks/lesson02_exercises.ipynb, datasets/}
│   ├── lesson03_feature_engineering/      # Section 3
│   │   ├── theory/{notes/Lecture3 - Student Pack.md, slides/*.pptx}
│   │   └── practical/task/{README.md, notebooks/lesson03_exercises.ipynb, datasets/}
│   ├── lesson04_model_validation/         # Section 4
│   │   ├── theory/{notes/Lecture4 - Student Pack.md, slides/*.pptx}
│   │   └── practical/task/{README.md, notebooks/lesson04_exercises.ipynb, datasets/}
│   └── lesson05_research_grade_ml/        # Section 5
│       ├── theory/{notes/Lecture5 - Student Pack.md, slides/*.pptx}
│       └── practical/task/{README.md, notebooks/lesson05_exercises.ipynb, datasets/}
└── solutions/                            # Reference solutions (mirrors lessons/)
    ├── README.md
    └── lessonNN_<topic>/
        ├── notebooks/                     # lessonNN_solutions.ipynb
        └── reports/                       # example deliverable
```

## Lesson ⇄ Section Mapping

| Lesson | Section | Topic | Deliverable |
|---|---|---|---|
| `lesson00_prerequisites` | — | Environment & data setup (run once, before Lesson 1) | Populated shared data cache |
| `lesson01_biological_question` | 1 | From Biological Question to ML Problem | Project specification |
| `lesson02_baseline_model` | 2 | Building the First Predictive Model | Baseline model report |
| `lesson03_feature_engineering` | 3 | Feature Engineering & Biological Representation | Feature engineering notebook |
| `lesson04_model_validation` | 4 | Improving and Validating Models | Model comparison report |
| `lesson05_research_grade_ml` | 5 | Research-Grade Machine Learning | Research-style report + pipeline |

## Conventions

- **`lessons/lessonNN_<topic>/`** — one directory per lesson/section; zero-padded number + snake_case topic.
- **`theory/notes/`** — student notes (`Lecture N - Student Pack.md`).
- **`theory/slides/`** — slide decks (`Lecture N - <title>.pptx`).
- **`practical/task/`** — student-facing exercises (`README.md`, `notebooks/lessonNN_exercises.ipynb`, `datasets/`). One README per lesson, at `practical/task/README.md`.
- **`solutions/lessonNN_<topic>/`** — reference solutions mirroring the lessons.
- **`lessons/lesson00_prerequisites/`** — one-time setup: creates the `ml26` conda env (+ Jupyter
  kernel) and downloads the data. Run before Lesson 1.
- **Data hand-off between lessons** — there is no shared code module. **Lesson 1** shows the loader
  functions inline and saves a `lesson01_cohort` checkpoint; **Lessons 2–5 load it** (Lesson 3 saves
  a `lesson03_features` checkpoint for Lessons 4–5). Checkpoints live in `datasets/derived/`
  (git-ignored). A lesson raises a clear "run Lesson N first" error if its checkpoint is missing.

## Student vs instructor materials

What students receive is tracked in git: the top `README.md`, `requirements.txt`, `lessons/overview.md`,
each lesson's `Lecture N - Student Pack.md` + `.pptx` + `practical/task/` (README + exercise notebook),
and the `solutions/` tree. Instructor-only build materials — speaker transcripts, `slides_source.md`,
`speaker_notes.md`, `instructor_talking_points.md`, `assessment_questions.md`, etc. — are kept local via
`.git/info/exclude` and are **not** pushed. Runtime data caches (`datasets/`) and local virtualenvs are
git-ignored via `.gitignore`.

## Adding a New Lesson

```bash
LESSON=lesson06_<topic>
mkdir -p lessons/${LESSON}/theory/{slides,notes}
mkdir -p lessons/${LESSON}/practical/task/{notebooks,datasets}
mkdir -p solutions/${LESSON}/{notebooks,reports}
touch lessons/${LESSON}/practical/task/datasets/.gitkeep solutions/${LESSON}/reports/.gitkeep
```

Then add `practical/task/README.md`, the student notes (`Lecture N - Student Pack.md`) in
`theory/notes/`, the deck (`Lecture N - <title>.pptx`) in `theory/slides/`, the exercise notebook
(`notebooks/lessonNN_exercises.ipynb`), and the reference solution under `solutions/`. Commit:

```bash
git add lessons/${LESSON} solutions/${LESSON}
git commit -m "${LESSON}: add theory and exercises"
```
