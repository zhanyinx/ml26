# Lesson 01 — Introduction to Machine Learning

## Learning Objectives

By the end of this lesson you will be able to:

- Explain what machine learning is and how it differs from classical programming.
- Distinguish between supervised, unsupervised, and reinforcement learning.
- Describe the typical machine learning workflow.
- Identify common pitfalls such as overfitting and data leakage.

---

## 1. What is Machine Learning?

Machine learning (ML) is a sub-field of artificial intelligence that enables
systems to **learn patterns from data** rather than following hand-crafted rules.

> "A computer program is said to learn from experience E with respect to some
> task T and performance measure P if its performance on T, as measured by P,
> improves with experience E."  
> — Tom Mitchell, 1997

### Classical Programming vs. Machine Learning

| Classical Programming | Machine Learning |
|---|---|
| Rules + Data → Output | Data + Output → Rules |
| Explicit logic | Learned patterns |
| Fragile with edge cases | Generalises from examples |

---

## 2. Types of Machine Learning

### 2.1 Supervised Learning

The model is trained on **labelled** examples `(x, y)` and learns a mapping
`f: x → y`.

- **Regression** — continuous output (e.g., house price prediction).
- **Classification** — discrete output (e.g., spam detection).

### 2.2 Unsupervised Learning

The model is trained on **unlabelled** data and must discover structure.

- **Clustering** — group similar observations (e.g., customer segmentation).
- **Dimensionality reduction** — compress features (e.g., PCA).

### 2.3 Reinforcement Learning

An **agent** interacts with an environment and learns a policy by maximising
cumulative rewards.

---

## 3. The ML Workflow

```
1. Define the problem
2. Collect & explore data (EDA)
3. Pre-process & engineer features
4. Select & train a model
5. Evaluate performance
6. Tune hyper-parameters
7. Deploy & monitor
```

---

## 4. Key Concepts

### Train / Validation / Test Split

| Split | Purpose |
|---|---|
| Training | Fit model parameters |
| Validation | Tune hyper-parameters |
| Test | Unbiased final evaluation |

A typical ratio is **70 / 15 / 15** or **80 / 10 / 10**.

### Overfitting and Underfitting

- **Underfitting (high bias)**: the model is too simple to capture the signal.
- **Overfitting (high variance)**: the model memorises noise in the training set.
- **Remedies**: cross-validation, regularisation, more data, simpler model.

### Evaluation Metrics (preview)

| Task | Common Metrics |
|---|---|
| Regression | MSE, RMSE, MAE, R² |
| Classification | Accuracy, Precision, Recall, F1, AUC-ROC |

---

## 5. Further Reading

- Géron, A. (2022). *Hands-On Machine Learning with Scikit-Learn, Keras & TensorFlow* (3rd ed.).
- Bishop, C. M. (2006). *Pattern Recognition and Machine Learning*.
- [Scikit-learn documentation](https://scikit-learn.org/stable/)
