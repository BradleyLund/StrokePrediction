# Class Imbalance

## What it is
**Class imbalance** means one outcome is much rarer than the other.

In our data:
- 4,861 patients did **not** have a stroke (95.1%)
- 249 patients **did** have a stroke (4.9%)

The rare outcome (stroke = 1) is called the **positive class** or **minority class**. "Positive" here means "the thing we're looking for". It doesn't mean "good".

## Why it's a trap: the accuracy problem
**Accuracy** = the percentage of predictions that are correct.

Imagine a "model" that ignores the data and always says **"no stroke"**:
- It's right for all 4,861 non-stroke patients.
- Accuracy = 4,861 / 5,110 = **95.1%**

That sounds great, but the model is useless. It finds **zero** stroke patients. So on imbalanced data, **accuracy is a misleading metric**. Saying this clearly in an interview is a strong signal that you understand the problem.

Instead, we'll use metrics that focus on how well we find the rare positives: **recall**, **precision**, **PR AUC**, and "how many strokes are in the top N". Each gets its own wiki page when we reach it.

## Other consequences
- **Splitting the data:** with only 249 positives, a random split could by chance put very few strokes in the test set. We use a **stratified** split, which keeps the 4.9% ratio the same in every piece.
- **Training:** some models just learn to predict "no" for everyone. Common fixes are **class weights**, which tell the model a missed stroke counts more, and **resampling** techniques. We'll cover these when we build models.
- **Small numbers = noisy results:** a test set might only contain about 50 strokes. One or two extra correct predictions can move the metrics a lot. That's why we'll use **cross-validation**, which tests the model several times on different slices of the data.

## Why it matters for this project
This is probably *the* central idea of the whole exercise. Nearly every decision we make later (metrics, splitting, model settings, threshold) is shaped by the fact that strokes are rare.
