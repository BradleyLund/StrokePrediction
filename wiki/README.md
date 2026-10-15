# Data Science Wiki

My own notes on the data science concepts I learn while working through the stroke-risk take-home.
Each page explains one idea in plain English, with a "Why it matters for this project" section.

## How to use this wiki
- Pages are added as we come across each concept, not all at once.
- If something on a page doesn't make sense yet, that's fine. Come back once we've used it in code.
- Every acronym is spelled out in [glossary.md](glossary.md).

## Pages

### Foundations
- [The data science workflow](the-data-science-workflow.md): the steps every project follows
- [Tools: Python, notebooks and environments](tools-python-notebooks-environments.md): what each tool is for
- [Classification, features and targets](classification-features-targets.md): what "predicting a stroke" actually means
- [Class imbalance](class-imbalance.md): why only 5% of patients having a stroke changes everything

### Step 1: Exploring the data
- [Exploratory data analysis (EDA)](exploratory-data-analysis.md): the standard first checks, and how to read `describe()`
- [pandas basics](pandas-basics.md): the pandas commands we use, and data types
- [Missing and suspicious data](missing-and-suspicious-data.md): disguised missing values, informative missingness, outliers
- [Hypotheses and confounding](hypotheses-and-confounding.md): testing your theories, and how a hidden third factor (like age) can fool you

### Step 2: Cleaning the data
- [Data cleaning](data-cleaning.md): the cleaning log, our decisions, and why some cleaning waits for the pipeline
- [Scoping the population](scoping-the-population.md): deciding who the model is for (adults only), and "easy negatives"

### Step 3: Visualising the data
- [Charts for EDA](charts-for-eda.md): which chart for which question, reading box plots, binning, cautions

### Step 4: Baseline model
- [Train / test split](train-test-split.md): why we hide patients from the model, stratification, random_state
- [Preprocessing and pipelines](preprocessing-and-pipelines.md): imputation, scaling, one-hot encoding, and why pipelines prevent leakage
- [Logistic regression](logistic-regression.md): how it works, reading coefficients, and the 0.5 trap
- [Evaluation metrics](evaluation-metrics.md): confusion matrix, precision/recall, ROC AUC, PR AUC, top-N, lift, gains chart

### Domain knowledge
- [Predicting insurance claim denials](claim-denial-prediction.md): what's on a claim, why claims get denied, and features a denial model could use
- [ML ideas for eligibility verification](eligibility-ml-ideas.md): rules vs models, plan assignment, and other eligibility use cases

### Coming up (added as we reach them)
- Cross-validation
- Tree-based models (random forest / gradient boosting)
- Choosing a threshold
- Data leakage

## Reference
- [Glossary of terms and acronyms](glossary.md)
