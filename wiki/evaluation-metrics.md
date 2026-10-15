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

### ROC AUC
**ROC** = Receiver Operating Characteristic. **AUC** = Area Under the Curve.

The intuitive meaning: **pick one random stroke patient and one random non-stroke patient. ROC AUC is the probability that the model gives the stroke patient the higher score.**
- 0.5 = no better than a coin flip
- 1.0 = perfect ranking
- Around 0.8 = good for health data like this

**Its weakness on imbalanced data:** it gives as much credit for ranking easy negatives low (healthy 25-year-olds) as for ranking the high-risk patients well. That can make a model look better than it is. Remember the "easy negatives" from [scoping-the-population.md](scoping-the-population.md).

### PR AUC (Average Precision)
The area under the **Precision–Recall curve**. In scikit-learn it's `average_precision_score`. It focuses only on how well the **positives** are found and ranked, so it's **more informative when positives are rare**.

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

## Which metric for what
| Purpose | Metric |
|---|---|
| Comparing models (technical) | **PR AUC** (primary) and ROC AUC (familiar, widely reported) |
| Choosing a threshold / call volume | **Precision and recall at N**, and the confusion matrix |
| Explaining to non-technical people | **"Call N patients → reach X stroke patients"** and the gains chart |
