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
│   ├── lesson01_biological_question/      # Section 1
│   │   ├── theory/
│   │   │   ├── notes/
│   │   │   │   └── Lecture1 - Student Pack.md
│   │   │   └── slides/
│   │   │       └── Lecture1 - From Biological Question to ML Problem.pptx
│   │   └── practical/task/
│   │       ├── README.md
│   │       ├── notebooks/{README.md, lesson01_exercises.ipynb}
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
| `lesson01_biological_question` | 1 | From Biological Question to ML Problem | Project specification |
| `lesson02_baseline_model` | 2 | Building the First Predictive Model | Baseline model report |
| `lesson03_feature_engineering` | 3 | Feature Engineering & Biological Representation | Feature engineering notebook |
| `lesson04_model_validation` | 4 | Improving and Validating Models | Model comparison report |
| `lesson05_research_grade_ml` | 5 | Research-Grade Machine Learning | Research-style report + pipeline |

## Conventions

- **`lessons/lessonNN_<topic>/`** — one directory per lesson/section; zero-padded number + snake_case topic.
- **`theory/notes/`** — student notes (`Lecture N - Student Pack.md`).
- **`theory/slides/`** — slide decks (`Lecture N - <title>.pptx`).
- **`practical/task/`** — student-facing exercises (`README.md`, `notebooks/lessonNN_exercises.ipynb`, `datasets/`).
- **`solutions/lessonNN_<topic>/`** — reference solutions mirroring the lessons.
