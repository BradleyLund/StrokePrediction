# Cross-Validation

## The problem
Our test set has only **49 strokes**. One unlucky split could make a model look much better or worse than it really is. And if we compare several models on the test set and pick the winner, we're quietly **fitting to the test set**. Its score would no longer be an honest "final exam".

## The solution: k-fold cross-validation
Use only the **training set**, and split it into **k** equal parts called **folds** (we use k = 5):

```
Training set (3,402 patients, 198 strokes), cut into 5 folds:

Round 1:  [VALID] [train] [train] [train] [train]  → score 1
Round 2:  [train] [VALID] [train] [train] [train]  → score 2
Round 3:  [train] [train] [VALID] [train] [train]  → score 3
Round 4:  [train] [train] [train] [VALID] [train]  → score 4
Round 5:  [train] [train] [train] [train] [VALID]  → score 5

Final CV score = average of the 5 scores (plus their spread)
```

Each round, the model trains on 4 folds and is scored on the 5th, the **validation fold**, which it hasn't seen. Every patient gets used for validation exactly once.

**What you get:**
- A **more reliable** score: an average over 5 different "exams" rather than 1.
- A measure of **noise**: the **standard deviation** (std) of the 5 scores. If two models differ by less than the std, the difference is probably just luck.

## Stratified k-fold
`StratifiedKFold` keeps the stroke rate the same in every fold, for the same reason we stratified the train/test split. With about 40 strokes per fold, that matters.

`shuffle=True, random_state=42` shuffles the patients before cutting the folds, in a repeatable way.

## The full workflow
```
All adults
├── Training set (80%) ──► cross-validation: compare models, choose settings
└── Test set (20%) ──────► touched ONCE, at the very end, on the chosen model
```

## Confusing naming alert
scikit-learn's `cross_validate` labels the validation-fold scores as **`test_...`** (e.g. `test_roc_auc`). Inside cross-validation, that "test" means **the validation fold**, *not* our sealed test set. Our real test set isn't involved at all.

## Reading the comparison table
Column names follow the pattern `where_metric`:
| Column | Meaning |
|---|---|
| `train_roc_auc` | ROC AUC on the patients the model **learned from** ("practice exam it had already seen") |
| `cv_roc_auc` | ROC AUC on the validation folds, i.e. patients it **hadn't seen** (averaged over 5 rounds). **This is the one that matters** |
| `cv_roc_auc_std` | How much that score varied across the 5 rounds. A measure of **noise** |
| `..._pr_auc` | Same three ideas, for PR AUC |

## Deciding whether one model really beats another
1. **Compare CV scores only.** Train scores just tell you about overfitting.
2. **Work out the difference** between the two models' CV scores.
3. **Compare the difference to the std.** If the gap is smaller than the std, the models are **tied**: the difference is within the noise.
4. **Check fold by fold** for a clearer picture: does one model win in most rounds?
5. **If tied, choose the simpler, more interpretable model.**

Example from this project:
| | CV ROC AUC | std | CV PR AUC | std |
|---|---|---|---|---|
| Logistic regression | 0.822 | 0.022 | 0.231 | 0.054 |
| Random forest (min 20/leaf) | 0.810 | 0.026 | 0.215 | 0.040 |
| Difference | 0.012 | | 0.016 | |

The differences (0.012 and 0.016) are smaller than the stds (about 0.02–0.05), so it's a **tie**, with logistic regression slightly ahead. Fold by fold, LR had the higher ROC AUC in 4 of 5 rounds. Conclusion: the random forest does **not** beat the baseline, so choose logistic regression.

## Train score vs CV score: spotting overfitting
`return_train_score=True` also reports how well the model scores **on the data it trained on**. Comparing the two is the standard overfitting check:
- Train ≈ CV → the model learned general patterns. 👍
- Train ≫ CV (e.g. 1.00 vs 0.77) → the model **memorised** the training patients. 👎 See [overfitting.md](overfitting.md).
