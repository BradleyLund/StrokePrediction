# Classification, Features and Targets

## Machine learning in one sentence
You show a computer lots of past examples where you already know the answer, and it learns a pattern it can use to guess the answer for new examples.

This is called **supervised learning**. It's "supervised" because the examples come with the right answers attached, like a teacher marking homework.

## Features vs target
In our CSV, each **row** is one patient. The columns split into two kinds:

- **Target** (also called the *label*, the *outcome*, or **y**): the thing we want to predict. Here it's `stroke` (1 = had a stroke, 0 = didn't).
- **Features** (also called *predictors*, *variables*, or **X**): the information we use to make the prediction. Here that's age, hypertension, glucose, BMI, smoking status, and so on.

`id` is **neither**. It's just a label for each patient, with no medical meaning. If we let the model use it, the model could pick up meaningless patterns, so we drop it.

## Classification vs regression
- **Classification** predicts a *category* (stroke / no stroke, spam / not spam). ← this is our problem
- **Regression** predicts a *number* (house price, temperature).

Our problem is **binary classification**, because there are exactly two categories.

## Probabilities, not just yes/no
Most classifiers don't just say "yes" or "no". They output a **probability**, for example "this patient has a 0.23 (23%) chance of stroke". We then pick a **threshold** (a cutoff), such as "flag anyone above 0.15".

That's exactly what the outreach team needs:
1. **Sort** patients by predicted probability, highest first. That gives the call list.
2. Choose a **threshold**, or simply "call the top N each month", based on capacity.

## Why it matters for this project
The question "who to contact first" is a **ranking** problem, built on top of a classification model's probabilities. Keep that in mind when we choose metrics.
