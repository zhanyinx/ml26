# Lesson 02 — Linear Regression: Exercises

## Overview

In this practical session you will implement and evaluate linear regression
models, exploring the effect of regularisation on model performance.

## Prerequisites

- Python 3.9+
- `numpy`, `pandas`, `matplotlib`, `scikit-learn`

Install with:

```bash
pip install numpy pandas matplotlib scikit-learn
```

## Exercises

Open `notebooks/lesson02_exercises.ipynb` and work through the tasks below.

### Task 1 — Load and Explore the Dataset

- Load the dataset from `datasets/`.
- Visualise the relationship between each feature and the target variable using
  scatter plots.
- Compute the correlation matrix and identify the most predictive features.

### Task 2 — Ordinary Least Squares (OLS)

- Implement OLS **from scratch** using the closed-form solution:
  `w* = (XᵀX)⁻¹ Xᵀy`
- Verify your implementation against `sklearn.linear_model.LinearRegression`.
- Report MSE, RMSE, MAE, and R² on the test set.

### Task 3 — Gradient Descent

- Implement batch gradient descent for linear regression.
- Plot the training loss curve over iterations.
- Experiment with different learning rates (α ∈ {0.001, 0.01, 0.1}) and
  observe the effect on convergence.

### Task 4 — Regularisation

- Train Ridge, Lasso, and Elastic Net models using scikit-learn.
- Use cross-validation to select the best regularisation strength λ.
- Compare the learned coefficient vectors for each method — which coefficients
  are set to (near) zero by Lasso?

### Task 5 — Residual Analysis

- Plot residuals vs. predicted values and a Q-Q plot of residuals.
- Check for heteroscedasticity and discuss any violations of the OLS assumptions
  you observe.

### Task 6 — Reflection

Answer the following questions in a markdown cell:

1. When would you prefer Lasso over Ridge regression? Give a concrete example.
2. What happens to the OLS solution when XᵀX is singular?
3. How does gradient descent behave when the learning rate is too large?

## Submission

Save your completed notebook as `lesson02_exercises_<your_name>.ipynb` and
submit it according to the course instructions.
