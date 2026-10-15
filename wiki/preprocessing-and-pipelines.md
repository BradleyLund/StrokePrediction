# Preprocessing and Pipelines

Models only understand **numbers**, with **no missing values**. **Preprocessing** turns our cleaned table into that format.

## The three preprocessing jobs

### 1. Imputation: filling missing values
`SimpleImputer(strategy="median")` replaces missing values with the column's median. It **learns** the median from the training data during `.fit()`, then uses that same number to fill gaps in the test data.

### 2. Scaling: putting numbers on a similar scale
Age runs 18–82, glucose 55–270, BMI 11–60. Some models (including logistic regression) work better, and are easier to interpret, when numbers are on a comparable scale.

`StandardScaler` converts each value to "how many **standard deviations** above or below the average is this?"
- An average-aged patient becomes 0.
- A patient one standard deviation older than average becomes +1.

This is called **standardising**. The result is sometimes called a **z-score**.

### 3. Encoding: turning categories into numbers
A model can't do maths on "Self-employed". **One-hot encoding** creates one new 0/1 column per category:

| work_type | → | work_type_Govt_job | work_type_Private | work_type_Self-employed | work_type_Never_worked |
|---|---|---|---|---|---|
| Private | | 0 | 1 | 0 | 0 |
| Self-employed | | 0 | 0 | 1 | 0 |

**Why not just number them 1, 2, 3, 4?** That would tell the model "Self-employed (3) is three times Govt_job (1)", which is meaningless. One-hot avoids inventing a fake order.

`handle_unknown="ignore"`: if the test data contains a category the model never saw in training, it gets all zeros instead of an error. That fixes the gender "Other" worry from the parking lot.

## ColumnTransformer: different jobs for different columns
Number columns need imputing and scaling, category columns need one-hot encoding, and the 0/1 columns can pass straight through. A `ColumnTransformer` routes each group of columns to the right treatment, then glues the results back together.

## Pipeline: chaining it all with the model
A `Pipeline` bundles preprocessing **and** the model into a single object:
```
raw features ─► ColumnTransformer (impute, scale, encode) ─► LogisticRegression ─► prediction
```

### Why pipelines matter (leakage again)
- Calling `pipeline.fit(X_train, y_train)` makes **every** step learn from the training data only: the medians, the averages for scaling, the list of categories, and the model itself.
- Calling `pipeline.predict_proba(X_test)` **applies** those learned values to the test data. It never learns anything from it.

So the median used to fill test patients' BMI comes from training patients only, which is exactly the rule from [data-cleaning.md](data-cleaning.md). It also means there's one object to save and reuse on next month's patients.

## fit vs transform vs predict
| Method | Meaning |
|---|---|
| `.fit(X, y)` | **Learn** from this data (medians, scales, model weights) |
| `.transform(X)` | **Apply** what was learned to change the data |
| `.predict(X)` | Give a yes/no (1/0) answer using a 0.5 cutoff |
| `.predict_proba(X)` | Give the **probability** of each class. This is what we want for ranking |
