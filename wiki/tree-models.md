# Tree-Based Models

## Decision tree: the building block
A **decision tree** is a flowchart of yes/no questions learned from the data:

```
                 Age ≥ 67?
               /           \
             no             yes
             /                \
     Glucose ≥ 160?       Hypertension?
       /      \             /        \
    1% risk  6% risk    10% risk   22% risk
```
- Each question is a **split**. The ends are **leaves**, and each leaf's prediction is the stroke rate of the training patients who landed there.
- Trees capture **interactions** naturally: "glucose matters more for older patients". Logistic regression adds up each feature's effect separately, so it misses interactions unless you build them in by hand.
- A single tree **overfits easily**. If it keeps splitting, every leaf ends up as one patient.

## Random forest
A **random forest** = many trees (e.g. 300–500), each trained on:
- a random **resample** of the patients (called **bootstrapping**), and
- a random subset of features at each split.

The forest's prediction is the **average** of all the trees. Individual trees make different mistakes, and averaging cancels much of the noise. This is called an **ensemble**: a model made of many models.

Key hyperparameters:
| Setting | Meaning |
|---|---|
| `n_estimators` | Number of trees. More is steadier but slower |
| `min_samples_leaf` | Minimum patients per leaf. Higher means simpler trees and less overfitting |
| `max_depth` | Maximum number of questions in a row |

## Gradient boosting
Also builds many trees, but **one after another**: each new small tree focuses on correcting the mistakes of the trees so far. It's often the strongest model type for **tabular data** (spreadsheet-style rows and columns), and is what libraries like XGBoost and LightGBM do.

Key hyperparameters:
| Setting | Meaning |
|---|---|
| `n_estimators` | Number of trees (correction rounds) |
| `learning_rate` | How much each new tree is allowed to correct. Smaller is more cautious and needs more trees |
| `max_depth` | Size of each tree. Small (2–3) is usual for boosting |

## Do trees need scaling?
No. A tree only asks "is age ≥ 67?", so the scale of the numbers doesn't matter. We keep the same preprocessing pipeline anyway, for simplicity. Scaling doesn't hurt trees.

## Trees vs logistic regression: which to choose?
| | Logistic regression | Tree ensembles |
|---|---|---|
| Interpretability | High (coefficients) | Lower (needs extra tools to explain) |
| Interactions / curves | Must be added by hand | Found automatically |
| Overfitting risk with little data | Low | Higher, needs tuning |
| Typical result on small, simple tabular data | Often just as good | Better when patterns are complex and data is plentiful |

## The data ceiling
When **very different model types** (a linear model, a forest of averaged trees, sequentially boosted trees) all land on about the same score (here ROC AUC ≈ 0.81–0.82), the limit is probably **the data, not the algorithm**. The features simply don't contain more signal. More tuning gives **diminishing returns**. The bigger gains come from **better data**: more relevant features, more positive cases, cleaner timing. In data science shorthand: *"better data beats fancier algorithms."*

**If a complex model doesn't clearly beat the simple one, choose the simple one.** It's easier to explain, easier to trust, and easier to maintain. That's not a failure. It's a finding.
