# Logistic Regression

## What it is
Despite the name, **logistic regression** is a **classification** model. It predicts the **probability** that something is true (here: stroke = 1).

It's the classic **baseline** for yes/no problems, because it's:
- **Simple and fast**
- **Interpretable:** you can see how much each feature pushes risk up or down
- **Well understood**, especially in healthcare, where clinicians and statisticians have used it for decades

## How it works (the intuition)
1. Give each feature a **weight** (also called a **coefficient**). Features that raise risk get positive weights, and features that lower it get negative weights.
2. For a patient, add up: `score = intercept + weight₁×age + weight₂×hypertension + ...`
3. Squash the score into a probability between 0 and 1 using an S-shaped curve called the **sigmoid** (or logistic) function. That's where the name comes from.

```
probability
  1 ┤                 ╭──────
    │               ╭─╯
0.5 ┤             ╭─╯
    │           ╭─╯
  0 ┤──────────╯
    └────────────────────────── score
         low risk ◄──► high risk
```

**Training** (`.fit`) means finding the weights that best match the real outcomes in the training data.

## Reading the coefficients
Because we standardised the number features, the coefficients are roughly comparable in size:
- **Bigger positive** = pushes stroke risk up more
- **Negative** = pushes risk down
- **Near zero** = the model barely uses it, *once all the other features are taken into account*

That last part is how a model handles confounding. In EDA, `ever_married` looked like a risk factor. In the model, alongside age, its coefficient is close to zero, because age explains it.

**Caution:** coefficients show **association, not causation**. And for 0/1 or one-hot columns, the size isn't directly comparable to the standardised number columns.

## Big coefficient ≠ big impact on the model overall
`bmi_missing` has one of the largest coefficients (1.21), yet removing it barely changed performance (CV PR AUC 0.23 → 0.20). Three reasons:

1. **It applies to few patients.** Only about 4% of adults have missing BMI. A big push for a few patients changes little of the overall ranking.
2. **Scale differs.** For a 0/1 flag, the coefficient is the effect of going from 0 to 1. For standardised age, it's the effect *per standard deviation* (about 18 years). Adult ages span roughly 3.6 standard deviations, so age's total effect is about 1.30 × 3.6 ≈ **4.7**, nearly 4× the flag's 1.21.
3. **Overlapping information.** Missing-BMI patients are also older, with more hypertension, heart disease and higher glucose. Remove the flag and those features **pick up much of the slack**. The information is partly *redundant*.

Useful phrase: *"the coefficient tells you how much a feature matters for the patients who have it. Removing it tells you how much the model as a whole depends on it."*

## max_iter
The model finds its weights through repeated small improvements (**iterations**). `max_iter=1000` allows up to 1,000 steps. The default of 100 sometimes isn't enough, and you'd get a "failed to converge" warning.

## The 0.5 trap
`.predict()` says "stroke" only when the probability is above **0.5**. With a 5.8% base rate, almost no patient reaches 50%, so `.predict()` flags almost nobody. That's why we work with `.predict_proba()` and choose our own **threshold** based on what the outreach team needs.

## Class weights (an option we didn't use for the baseline)
`LogisticRegression(class_weight="balanced")` tells the model to treat each missed stroke as more costly, to make up for strokes being rare. It pushes predicted probabilities upward, so they stop being realistic risk estimates. The **ranking** of patients barely changes, though, and ranking is what outreach needs. We kept the baseline simple and unweighted, so its probabilities stay realistic.
