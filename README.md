# ml26 — Classical Machine Learning for PhD Students

A course combining theory and hands-on practical sessions covering classical machine learning methods.

---

## Repository Structure

```
ml26/
├── README.md
├── .gitignore
└── lessons/
    ├── lesson01_intro_to_ml/
    │   ├── theory/
    │   │   ├── slides/          # Slide decks (PDF / PPTX)
    │   │   └── notes/           # Written notes and summaries (Markdown)
    │   └── practical/
    │       ├── task/            # ✅ Committed — student-facing exercises
    │       │   ├── README.md
    │       │   ├── datasets/
    │       │   └── notebooks/
    │       └── solution/        # ❌ Gitignored — instructor-only solutions
    │           ├── notebooks/
    │           └── README.md
    ├── lesson02_linear_regression/
    │   ├── theory/
    │   │   ├── slides/
    │   │   └── notes/
    │   └── practical/
    │       ├── task/
    │       │   ├── README.md
    │       │   ├── datasets/
    │       │   └── notebooks/
    │       └── solution/        # ❌ Gitignored — instructor-only solutions
    │           ├── notebooks/
    │           └── README.md
    └── lessonNN_<topic>/        # Add new lessons following the same pattern
        └── ...
```

---

## Naming Conventions

| Item | Convention | Example |
|---|---|---|
| Lesson folder | `lessonNN_<snake_case_topic>` | `lesson03_decision_trees` |
| Exercise notebook | `lessonNN_exercises.ipynb` | `lesson01_exercises.ipynb` |
| Solution notebook | `lessonNN_solutions.ipynb` | `lesson01_solutions.ipynb` |
| Slide deck | `lessonNN_<topic>_slides.pdf` | `lesson01_intro_to_ml_slides.pdf` |
| Notes file | `lessonNN_notes.md` | `lesson01_notes.md` |
| Dataset | `<descriptive_name>.<ext>` | `iris.csv`, `boston_housing.csv` |

- `NN` is a zero-padded two-digit number (01, 02, …, 10, 11, …).
- Use **lowercase snake_case** for all folder and file names.

---

## Access Control

| Folder | Students | Instructor |
|---|---|---|
| `theory/` | ✅ Full access | ✅ Full access |
| `practical/task/` | ✅ Full access | ✅ Full access |
| `practical/solution/` | ❌ Never pushed to GitHub | ✅ Local only |

The `.gitignore` file contains the pattern `**/solution/` which prevents any
`solution/` directory at any depth from ever being committed or pushed.

---

## Instructor Guide — Managing Solutions

1. **Create the solution folder locally** (it will never be pushed):
   ```bash
   mkdir -p lessons/lessonNN_<topic>/practical/solution/notebooks
   ```

2. **Verify it is ignored** before committing:
   ```bash
   git status   # solution/ should not appear
   git check-ignore -v lessons/lessonNN_<topic>/practical/solution/
   ```

3. **Back up solutions** using a separate private repository or encrypted
   storage — do not rely solely on the local copy.

4. **Never force-add** a solution folder:
   ```bash
   # Do NOT run:
   git add -f lessons/.../solution/
   ```

---

## Adding a New Lesson

1. Create the folder skeleton:
   ```bash
   LESSON=lesson03_decision_trees
   mkdir -p lessons/${LESSON}/theory/{slides,notes}
   mkdir -p lessons/${LESSON}/practical/task/{notebooks,datasets}
   mkdir -p lessons/${LESSON}/practical/solution/notebooks   # local only
   ```

2. Add a `README.md` inside `practical/task/` describing the exercises.

3. Add theory notes in `theory/notes/` and upload slide PDFs to `theory/slides/`.

4. Commit only the tracked folders:
   ```bash
   git add lessons/${LESSON}/theory lessons/${LESSON}/practical/task
   git commit -m "lesson03: add decision trees theory and exercises"
   ```

---

## Environment Setup

```bash
# Create and activate a virtual environment
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt  # add this file when dependencies are defined

# Launch Jupyter
jupyter notebook
```

---

## Course Outline

| # | Topic | Status |
|---|---|---|
| 01 | Introduction to Machine Learning | ✅ Available |
| 02 | Linear Regression | ✅ Available |
| 03 | … | 🔜 Coming soon |

---

## License

Course materials © the authors. All rights reserved unless stated otherwise.
