# Lesson 0 — Prerequisites: Environment & Data Setup

**Run this once, before Lessons 1–5.**

This is not a graded lesson — it is the one-time setup step the rest of the course assumes is done:

1. **Creates the environment** — a dedicated **conda** environment named `ml26`, registered as a
   Jupyter kernel, with the scientific stack plus the data-download helpers (`requests`, `GEOparse`),
   from [`requirements.txt`](requirements.txt).
2. **Downloads the data into the shared cache** — **METABRIC** (~660 MB, cBioPortal datahub)
   and **GSE6532** (~180 MB, NCBI GEO).

## 1. Create the `ml26` conda environment

You need **conda**. If you don't have it, install **Miniconda** first (~5 min):
**https://docs.anaconda.com/miniconda/** (installers + per-OS instructions).

From the repository root:

```bash
conda create -n ml26 python=3.11 -y
conda activate ml26
pip install -r requirements.txt
python -m ipykernel install --user --name ml26 --display-name "Python (ml26)"
jupyter lab
```

The last `ipykernel` command registers the environment as a Jupyter kernel named **`ml26`**. Every
notebook in the course is set to that kernel, so in Jupyter pick **Python (ml26)** (kernel picker
top-right, or *Kernel → Change Kernel*).

## 2. Download the data

Open [`lesson00_prerequisites.ipynb`](lesson00_prerequisites.ipynb) with the **Python (ml26)** kernel
and **Run All**. Internet is required; the first METABRIC download takes a few minutes. When the final
cell lists the cached files, you're set.

## Why it exists

Every lesson resolves the **same** cache folder
(`lessons/lesson01_biological_question/practical/task/datasets/`), so the data is downloaded only
once, here. From there the lessons hand data forward via **checkpoints** in `datasets/derived/`:
Lesson 1 saves the prepared cohort, Lessons 2–5 load it (Lesson 3 saves an engineered-features
checkpoint for Lessons 4–5).

The cache and checkpoints are **git-ignored** — never commit them. You don't need to re-run this
notebook unless you delete the cache or move the repository.
