# Train / Test Split

## The problem it solves
A model can **memorise** the patients it learned from instead of learning general patterns. If you then test it on those same patients, it looks brilliant, but it's like a student who has seen the exam answers in advance.

What we actually care about is: **how well does it do on patients it has never seen?** Those are the patients the outreach team will use it on.

## The solution
Split the data into two parts *before* training:
- **Training set** (usually 70–80%): the model learns from these.
- **Test set** (usually 20–30%): locked away and used **once**, at the very end, to estimate real-world performance.

```
All adults (4,253)
├── Training set (80%) ─► 3,402 patients, 198 strokes  ← model learns here
└── Test set     (20%) ─►   851 patients,  49 strokes  ← final exam, used once
```

**Treat the test set as sealed.** If you keep checking test results while tweaking the model, you're slowly fitting the model to the test set, and its score stops being an honest estimate. Later, we'll use **cross-validation** *on the training set* to compare models, and keep the test set for the final check.

## Stratification
With only about 5.8% strokes, a purely random split could, by bad luck, put 30 strokes in the test set or 70. **Stratifying** (`stratify=y`) forces both parts to have the same stroke rate. With rare outcomes, always stratify.

## random_state: making it repeatable
The split is random. `random_state=42` fixes the "random seed", so you get **the same split every time** you run the notebook. That way your results don't change on every run, and anyone else running your code gets the same numbers. The number 42 itself doesn't matter. It's just a common convention (a reference to *The Hitchhiker's Guide to the Galaxy*).

## X and y
By convention:
- **X** (capital, because it's a table) = the features
- **y** (lowercase, because it's a single column) = the target

So `train_test_split` gives four pieces: `X_train, X_test, y_train, y_test`.

## The caveat: small test sets are noisy
Our test set has only **49 strokes**. If the model gets 2 or 3 more of them right or wrong, the metrics move noticeably. Keep that in mind when interpreting results, and it's part of why cross-validation helps.
