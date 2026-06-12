# Lesson 02 — Linear Regression

## Learning Objectives

By the end of this lesson you will be able to:

- Derive and interpret the linear regression model.
- Estimate parameters using Ordinary Least Squares (OLS) and gradient descent.
- Evaluate regression models with standard metrics.
- Apply regularisation (Ridge, Lasso) to prevent overfitting.

---

## 1. The Linear Model

Given an input vector **x** ∈ ℝᵈ and a scalar target y ∈ ℝ, the model is:

```
ŷ = w₀ + w₁x₁ + w₂x₂ + … + wdxd  =  xᵀw  (with bias absorbed)
```

- **w** ∈ ℝᵈ⁺¹ — weight vector (parameters to learn).
- The relationship between features and target is assumed to be **linear**.

---

## 2. Loss Function — Mean Squared Error

We minimise the average squared residual over N training examples:

```
L(w) = (1/N) Σᵢ (yᵢ − ŷᵢ)²
```

---

## 3. Ordinary Least Squares (OLS)

The closed-form solution that minimises MSE exactly:

```
w* = (XᵀX)⁻¹ Xᵀy
```

- **X** ∈ ℝᴺˣ⁽ᵈ⁺¹⁾ — design matrix.
- Requires XᵀX to be invertible (full column rank).
- Computational cost: O(d³) — impractical for very high-dimensional data.

---

## 4. Gradient Descent

An iterative optimisation algorithm suitable for large datasets:

```
w ← w − α ∇L(w)
∇L(w) = −(2/N) Xᵀ(y − Xw)
```

- **α** (learning rate) controls the step size.
- Variants: Batch GD, Stochastic GD (SGD), Mini-batch GD.

---

## 5. Evaluation Metrics

| Metric | Formula | Notes |
|---|---|---|
| MSE | (1/N) Σ(y − ŷ)² | Penalises large errors heavily |
| RMSE | √MSE | Same units as y |
| MAE | (1/N) Σ\|y − ŷ\| | More robust to outliers |
| R² | 1 − SS_res/SS_tot | Proportion of variance explained; 1 is perfect |

---

## 6. Regularisation

To combat overfitting when features are many or correlated:

### 6.1 Ridge Regression (L2)

```
L_ridge(w) = MSE + λ Σⱼ wⱼ²
```

- Shrinks all coefficients toward zero; never sets them exactly to zero.

### 6.2 Lasso Regression (L1)

```
L_lasso(w) = MSE + λ Σⱼ |wⱼ|
```

- Promotes **sparsity**: can set coefficients exactly to zero (feature selection).

### 6.3 Elastic Net (L1 + L2)

```
L_elastic(w) = MSE + λ₁ Σ|wⱼ| + λ₂ Σwⱼ²
```

---

## 7. Assumptions of Linear Regression

1. **Linearity** — the relationship between X and y is linear.
2. **Independence** — observations are independent.
3. **Homoscedasticity** — constant variance of residuals.
4. **Normality** — residuals are normally distributed (for inference).
5. **No multicollinearity** — features are not perfectly correlated.

Violations can be detected via residual plots and the Variance Inflation Factor (VIF).

---

## 8. Further Reading

- Hastie, T., Tibshirani, R., & Friedman, J. (2009). *The Elements of Statistical Learning* (2nd ed.). Chapters 2–3.
- [Scikit-learn: Linear Models](https://scikit-learn.org/stable/modules/linear_model.html)
