# Data Cleaning

## What it is
**Data cleaning** means turning raw data into something a model can safely learn from: fixing errors, dealing with missing values, and removing things that don't belong.

There's rarely one "correct" way to clean. What's assessed in an interview (and in a real job) is that each decision is:
1. **Deliberate:** you chose it for a reason
2. **Documented:** written down where others can see it
3. **Proportionate:** you didn't spend hours on something affecting 1 row

## The cleaning log
Keep a table of every issue, what you did, and why. It goes straight into your presentation.

| Issue | Decision | Reason |
|---|---|---|
| `id` column | Drop from features | Just a label, no medical meaning. The model could learn nonsense patterns from it |
| `gender` = "Other" (1 patient) | Drop that row | A model can't learn from 1 example, and it could cause errors if it only lands in the test data |
| `bmi` > 60 (13 patients) | Cap at 60 | Very likely data entry errors. Capping keeps the patients but limits the damage |
| `bmi` missing (201 patients) | Add a `bmi_missing` flag, then fill with the median **inside the model pipeline** | Missingness is strongly linked to stroke, so the flag keeps that signal. See the leakage note below |
| `smoking_status` = "Unknown" (30%) | Keep as its own category | Too many to drop. "Unknown" is a real state of the record |
| `age` < 1 (43 patients) | Keep as valid data, but... | Real infants (work_type = children), not errors |
| Patients under 18 (856, 2 strokes) | Exclude from modelling | Outreach program is for adults. See [scoping-the-population.md](scoping-the-population.md) |
| Duplicates | None found | Checked both full-row duplicates and repeated `id`s |

## Always work on a copy
```python
df_clean = df.copy()
```
This keeps the original `df` untouched, so you can always compare "before vs after" or start again. Without `.copy()`, pandas can sometimes change the original by accident.

## Why not fill missing BMI right now? (preview of an important idea)
Filling with the median sounds harmless. But **which** patients would that median come from? If we calculate it from *all* patients, the test patients (the ones we later use to judge the model) have helped shape the training data. That's a small form of **data leakage**.

The rule: **anything that "learns" from the data, even something as simple as a median, should learn from the training data only**, then be applied to the test data. scikit-learn's **pipelines** do this automatically, so we'll do the filling there. In this dataset the difference would be tiny, but showing you know the rule is a strong signal in an interview.

Things that don't learn from the data (dropping a column, capping at a fixed value of 60 we chose ourselves, adding a missing flag) are safe to do up front.

## Useful pandas for cleaning
| Code | What it does |
|---|---|
| `df.duplicated().sum()` | Count fully duplicated rows |
| `df[df["col"] != "x"]` | Keep rows where col is NOT "x" (`!=` means "not equal") |
| `df["col"].clip(upper=60)` | Cap values at 60. Missing values stay missing |
| `df["col"].isna().astype(int)` | 1 if missing, 0 if not. `.astype(int)` turns True/False into 1/0 |
| `df.drop(columns=["col"])` | Remove a column |
