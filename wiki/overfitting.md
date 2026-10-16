# Overfitting (and Underfitting)

## Overfitting
**Overfitting** is when a model **memorises** its training data, including the random noise, instead of learning patterns that generalise to new patients.

Analogy: a student who memorises last year's exam answers scores 100% on that paper and fails a new one.

**How to spot it:** a big gap between the **training score** and the **cross-validation (or test) score**.

| Model | Train ROC AUC | CV ROC AUC | Diagnosis |
|---|---|---|---|
| Logistic regression | 0.84 | 0.82 | Healthy, small gap |
| Random forest (default settings) | **1.00** | 0.77 | **Badly overfit**: perfect on training data, worse than LR on new data |

A training score of exactly 1.00 is a red flag, not a triumph.

**The fix in action:** forcing at least 20 patients per leaf (`min_samples_leaf=20`) narrowed the gap (train 0.91, CV 0.81) and **raised** the CV score from 0.77 to 0.81. Making the model *less* flexible made it better on new patients.

## Underfitting
The opposite: the model is **too simple** to capture the real patterns, so it scores poorly on *both* training and validation data.

## The balance: model complexity
```
score
  │          validation score
  │        ╭──────╮
  │      ╭─╯      ╰──╮
  │    ╭─╯           ╰──╮       ← too complex: overfits
  │  ╭─╯
  │──╯  ← too simple: underfits
  └─────────────────────────── model complexity
           ↑ sweet spot
```

## Why our data is prone to overfitting
Only **198 strokes** in training. A flexible model can easily "explain" each one individually with a very specific rule ("a 67-year-old self-employed rural man with glucose 228.69..."), and that rule won't apply to anyone else.

## How to reduce it
- **Simpler models** (logistic regression is naturally resistant)
- **Constrain the model** through its **hyperparameters**, e.g. require at least 20 patients in each tree leaf (`min_samples_leaf=20`), or limit tree depth (`max_depth`)
- **More data** (not available here)
- **Regularisation**: a built-in penalty on complexity. Logistic regression in scikit-learn applies this by default

## Hyperparameters vs parameters
- **Parameters** are learned *from the data* during `.fit()` (e.g. logistic regression's coefficients).
- **Hyperparameters** are settings *you choose before training* (e.g. `min_samples_leaf`, `max_depth`, `n_estimators`).

Choosing hyperparameters using cross-validation is called **tuning**. Never tune on the test set.
