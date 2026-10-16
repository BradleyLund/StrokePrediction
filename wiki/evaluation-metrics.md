# Evaluation Metrics

## Why accuracy is out
A "model" that always says "no stroke" is 94% accurate on our adults and finds **zero** strokes (see [class-imbalance.md](class-imbalance.md)). We need metrics that focus on **finding the rare positives**.

## The confusion matrix
Once you choose a **threshold** (e.g. "flag anyone with probability ≥ 0.10"), every patient lands in one of four boxes:

|  | **Predicted: no stroke** | **Predicted: stroke (flagged)** |
|---|---|---|
| **Actually no stroke** | **TN**: True Negative ✓ | **FP**: False Positive ✗ (a wasted call) |
| **Actually stroke** | **FN**: False Negative ✗ (a **missed** patient) | **TP**: True Positive ✓ (a useful call) |

Memory trick: the second word (Positive/Negative) is **what the model said**. The first word (True/False) is **whether it was right**.

## Precision and recall: the two key numbers
**Precision** = TP / (TP + FP)
> "Of the patients we **called**, what share were real stroke patients?"
> *Outreach meaning:* how efficient each call is.

**Recall** (also called **sensitivity**, or the **true positive rate**) = TP / (TP + FN)
> "Of **all** the real stroke patients, what share did we reach?"
> *Outreach meaning:* how much of the problem we're covering.

### The trade-off
- **Lower the threshold** → flag more people → recall goes **up**, but precision goes **down** (more wasted calls).
- **Raise the threshold** → flag fewer people → precision goes **up**, but recall goes **down** (more missed patients).

You can't maximise both at once. **Choosing the threshold is a business decision**: how many calls can the team make, and how bad is a missed patient compared with a wasted call? That's Step 6.

Baseline model on the test set (851 patients, 49 strokes):

| Threshold | Flagged | TP | Precision | Recall |
|---|---|---|---|---|
| 0.05 | 256 | 35 | 14% | 71% |
| 0.10 | 142 | 27 | 19% | 55% |
| 0.20 | 44 | 14 | 32% | 29% |
| 0.30 | 19 | 6 | 32% | 12% |

**F1 score** combines precision and recall into one number (their *harmonic mean*). It's common, but it assumes both matter equally, which isn't true for outreach. We don't rely on it.

## Threshold-free metrics: judging the ranking itself
Precision and recall change with the threshold. To compare **models** fairly, we want metrics that judge the whole ranking at once.

### How can a score have no threshold?
It doesn't skip thresholds. It **tries every possible threshold at once**, or equivalently, it only looks at the **order** of the patients. Sort the patients by predicted probability and the actual numbers stop mattering. Only the order counts.

Worked example: 6 patients sorted by risk, highest first. S = stroke, N = no stroke:
```
Position:  1   2   3   4   5   6
Actual:    S   N   S   N   N   S
```
**ROC AUC: count the pairs.** There are 3 strokes × 3 non-strokes = 9 stroke-vs-non-stroke pairs. In how many is the stroke ranked higher?
- Stroke at position 1 is above all 3 Ns → 3
- Stroke at position 3 is above the Ns at positions 4 and 5 → 2
- Stroke at position 6 is above none → 0
- ROC AUC = 5 / 9 = **0.56**

**PR AUC (average precision): walk down the list.** Each time you reach a stroke, write down the precision so far:
- 1st stroke at position 1: 1 of 1 called were strokes → 1.00
- 2nd stroke at position 3: 2 of 3 → 0.67
- 3rd stroke at position 6: 3 of 6 → 0.50
- Average precision = (1.00 + 0.67 + 0.50) / 3 = **0.72**

A perfect ranking (S S S N N N) scores 1.0 on both. Notice that average precision rewards finding strokes **early in the list**, which is exactly what limited call capacity needs.

### ROC AUC
**ROC** = Receiver Operating Characteristic. **AUC** = Area Under the Curve.

The intuitive meaning: **pick one random stroke patient and one random non-stroke patient. ROC AUC is the probability that the model gives the stroke patient the higher score.**
- 0.5 = no better than a coin flip
- 1.0 = perfect ranking
- Around 0.8 = good for health data like this

**Its weakness on imbalanced data:** it gives as much credit for ranking easy negatives low (healthy 25-year-olds) as for ranking the high-risk patients well. That can make a model look better than it is. Remember the "easy negatives" from [scoping-the-population.md](scoping-the-population.md).

### PR AUC (Average Precision)
The area under the **Precision–Recall curve**. In scikit-learn it's `average_precision_score`. It focuses only on how well the **positives** are found and ranked, so it's **more informative when positives are rare**.

**The PR curve:** recall on the x-axis, precision on the y-axis, with one point per possible threshold. Moving right means a lower threshold: more patients flagged, so more strokes found, but a smaller share of the flagged patients are real strokes. The random baseline is a **flat horizontal line at the stroke rate**. The left end of the curve is jagged, because there precision is based on only a handful of patients.

**Its baseline isn't 0.5.** A random model's PR AUC equals the **stroke rate** (≈ 0.058). So a PR AUC of 0.23 looks low, but it's about **4× better than random**. Always quote it next to its baseline.

## Top-N metrics: the most useful for outreach
The team will call a fixed number of patients, so measure exactly that: **sort by predicted probability, take the top N, and count the strokes.**

| Calls (top N) | Strokes reached | Precision at N | Recall at N | Random calling would reach |
|---|---|---|---|---|
| 50 | 16 | 32% | 33% | 2.9 |
| 100 | 21 | 21% | 43% | 5.8 |
| 200 | 33 | 17% | 67% | 11.5 |

- **Precision at N** (precision@N) = strokes in the top N ÷ N
- **Recall at N** (recall@N) = strokes in the top N ÷ all strokes
- **Lift** = precision at N ÷ the base rate. "How many times better than random?" (e.g. 32% ÷ 5.8% ≈ 5.5×)

## Cumulative gains chart
X-axis: % of patients called (highest risk first). Y-axis: % of all stroke patients reached.
- A random list is the diagonal: calling 20% of patients reaches 20% of strokes.
- A good model **bows up and to the left**. Our baseline reaches about **63% of strokes by calling the top 20%**.

It's the clearest single chart for a non-technical audience: *"call this many, reach this many."*

### Gains chart vs ROC curve: close cousins
| | Y-axis | X-axis |
|---|---|---|
| **Gains chart** | Recall (% of strokes reached) | % of **all** patients called = (TP + FP) / everyone |
| **ROC curve** | Recall, called **TPR** (True Positive Rate) here | **FPR** (False Positive Rate) = FP / (FP + TN) = % of **non-stroke** patients called |

The y-axis is identical. Only the x-axis differs: "everyone we called" vs "only the healthy people we called".

When positives are rare, those x-axes are almost the same number, because nearly everyone called is a non-stroke patient anyway:
> % called = 5.8% × TPR + 94.2% × FPR

So the two curves look very similar here. With a 50/50 outcome they'd look quite different.

**When to use which:** the ROC curve (and its area, ROC AUC) is the standard technical summary that every data scientist recognises. The gains chart's x-axis is **workload** (calls made), which maps directly onto the outreach team's capacity, so it's the better chart for the non-technical audience.

## Which metric for what
| Purpose | Metric |
|---|---|
| Comparing models (technical) | **PR AUC** (primary) and ROC AUC (familiar, widely reported) |
| Choosing a threshold / call volume | **Precision and recall at N**, and the confusion matrix |
| Explaining to non-technical people | **"Call N patients → reach X stroke patients"** and the gains chart |
