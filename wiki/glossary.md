# Glossary

Terms and acronyms in plain English. Added to as we go.

| Term | Meaning |
|---|---|
| **270 / 271** | The standard electronic eligibility inquiry (270) and the payer's response (271) |
| **AI** | Artificial Intelligence |
| **Anomaly detection** | Finding unusual patterns without labelled answers (a type of unsupervised learning) |
| **AUC** | Area Under the Curve: a single number summarising a performance curve (ROC or PR). See [evaluation-metrics.md](evaluation-metrics.md) |
| **Baseline** | The simple first model that every later model is compared against |
| **Binary classification** | Predicting one of exactly two outcomes (e.g. stroke / no stroke) |
| **Binning** | Cutting a continuous number (like age) into ranges/groups |
| **BMI** | Body Mass Index: weight (kg) ÷ height (m)². A rough measure of body fat |
| **Box plot** | A chart showing the median, middle 50% and outliers of a number. See [charts-for-eda.md](charts-for-eda.md) |
| **Calibration** | Whether predicted probabilities are honest: of things scored 80%, about 80% should be right |
| **Capping (clipping / winsorising)** | Replacing values above (or below) a limit with the limit itself |
| **Claim** | A bill a healthcare provider sends to an insurer asking to be paid for a service |
| **COB** | Coordination of Benefits: working out which insurer pays first when a patient has more than one |
| **Coefficient (weight)** | The number a model multiplies a feature by. Bigger positive = pushes the prediction up more |
| **ColumnTransformer** | A scikit-learn tool that applies different preprocessing to different columns |
| **Confounder** | A hidden third factor that affects both things you're comparing, making them look related. See [hypotheses-and-confounding.md](hypotheses-and-confounding.md) |
| **Confusion matrix** | A 2×2 table of TP, FP, FN and TN at a chosen threshold. See [evaluation-metrics.md](evaluation-metrics.md) |
| **CPT** | Current Procedural Terminology: codes for *what* was done to a patient |
| **CSV** | Comma-Separated Values: a plain-text table where commas separate the columns |
| **Cumulative gains chart** | Chart of % of patients called vs % of positives reached, compared with random |
| **Data cleaning** | Fixing errors, handling missing values and removing what doesn't belong, before modelling |
| **Data leakage** | When the model uses information it wouldn't really have at prediction time, so test results look better than real life |
| **DataFrame** | pandas' name for a table of data (rows and columns) |
| **Denial** | When an insurer refuses to pay a claim, with a reason code |
| **Domain knowledge** | Knowledge of the real-world subject the data comes from (here, medicine) |
| **dtype** | Data type of a column (int64 = whole numbers, float64 = decimals, object = text) |
| **Easy negatives** | Cases a model can trivially classify as negative (e.g. children for stroke). They make metrics look better than the model really is |
| **EDA** | Exploratory Data Analysis: looking at and summarising data before modelling |
| **F1 score** | The harmonic mean of precision and recall. A single combined number |
| **FPR** | False Positive Rate = FP / (FP + TN): the share of non-stroke patients wrongly flagged. The x-axis of a ROC curve |
| **Feature** | An input column used to make a prediction (also: predictor, variable, X) |
| **FN / FP** | False Negative (a missed stroke patient) / False Positive (a flagged patient who didn't have a stroke) |
| **Fuzzy matching** | Comparing text by how similar it is, so small differences like typos still match |
| **HCPCS** | Healthcare Common Procedure Coding System: procedure/supply codes, includes CPT |
| **HIPAA** | Health Insurance Portability and Accountability Act: US law protecting patient health information |
| **Hypothesis** | An idea that might explain a pattern, which you then test against the data |
| **ICD-10** | International Classification of Diseases, 10th revision: diagnosis codes, *why* the patient was seen |
| **Imbalanced data** | One outcome is much rarer than the other. See [class-imbalance.md](class-imbalance.md) |
| **Imputation** | Filling in missing values with an estimate (e.g. the median) |
| **Informative missingness** | When *whether* a value is missing is itself related to the outcome |
| **.ipynb** | IPython Notebook: the file format for Jupyter notebooks |
| **IQR** | InterQuartile Range: the distance between the 25th and 75th percentiles (the box in a box plot) |
| **Iteration** | One step of a model improving its weights during training |
| **JSON** | JavaScript Object Notation: a common text format for structured data |
| **Kernel** | The Python process running behind a notebook. It holds all your variables in memory |
| **Lift** | How many times better than random: precision at N ÷ the base rate |
| **LLM** | Large Language Model: an AI that reads and generates text (e.g. ChatGPT, Claude) |
| **Logistic regression** | A simple, interpretable model that predicts the probability of a yes/no outcome. See [logistic-regression.md](logistic-regression.md) |
| **MAR / MCAR / MNAR** | Missing At Random / Missing Completely At Random / Missing Not At Random. See [missing-and-suspicious-data.md](missing-and-suspicious-data.md) |
| **Median** | The middle value when sorted. Less affected by extreme values than the mean |
| **ML** | Machine Learning |
| **Multi-class classification** | Predicting one of three or more possible outcomes |
| **N/A / NaN** | Not Available / Not a Number: how missing values appear in data |
| **NPI** | National Provider Identifier: a unique ID for each US healthcare provider |
| **numpy array** | A grid of numbers without column names. Indexed with `[rows, columns]`. See [pandas-basics.md](pandas-basics.md) |
| **One-hot encoding** | Turning a category column into one 0/1 column per category |
| **Outlier** | A value far away from the rest |
| **Payer** | The insurer that pays the claim (Medicare, Medicaid, a commercial insurer) |
| **Percentile / quartile** | The value below which a given % of the data falls (25%, 50%, 75%) |
| **pip** | Python's package installer |
| **Pipeline** | A scikit-learn object that chains preparation steps and a model, so each step learns from training data only |
| **Positive class** | The outcome we're trying to detect (stroke = 1). It doesn't mean "good" |
| **PR AUC (Average Precision)** | Area under the Precision–Recall curve. Its random baseline equals the positive rate |
| **Precision** | Of the patients flagged, the share who really had a stroke: TP / (TP + FP) |
| **Precision@N / Recall@N** | Precision or recall when you flag only the top N patients by predicted risk |
| **Preprocessing** | Converting cleaned data into the all-numbers, no-gaps format a model needs |
| **Prior authorization** | Approval from the insurer required *before* certain services |
| **Proxy** | A feature that quietly stands in for another feature (e.g. "Unknown" smoking standing in for "young") |
| **random_state** | A fixed "seed" that makes random steps (like the split) give the same result every run |
| **RCM** | Revenue Cycle Management: the process of billing and getting paid for healthcare services |
| **Recall (sensitivity, TPR)** | Of all real stroke patients, the share flagged: TP / (TP + FN). TPR = True Positive Rate |
| **Regression** | Predicting a number (e.g. a dollar amount) rather than a category |
| **ROC AUC** | Probability a random positive is ranked above a random negative. 0.5 = random, 1.0 = perfect |
| **Rules-based system** | Logic written by hand (e.g. a mapping table), as opposed to rules learned from data |
| **Scaling / standardising** | Rescaling a number to "standard deviations from the average" so features are comparable (a z-score) |
| **Series** | pandas' name for a single column |
| **Sigmoid** | The S-shaped curve that turns a score into a probability between 0 and 1 |
| **sklearn** | Short name for the scikit-learn library |
| **Std (standard deviation)** | How spread out values are around the average |
| **Stratified split** | Splitting data so each piece keeps the same proportion of each class |
| **Supervised learning** | Learning from examples where the correct answer is already known |
| **Target population** | The group of people the model is built for and will be used on |
| **Target** | The column we're predicting (also: label, outcome, y) |
| **Test set** | Patients held back from training, used once at the end to estimate real-world performance |
| **Threshold** | The probability cutoff above which we flag a patient as "high risk" |
| **TN / TP** | True Negative (correctly not flagged) / True Positive (correctly flagged) |
| **Traceback** | Python's error report. Read it bottom-up: error message last, failing line marked with an arrow |
| **Training set** | The patients a model learns from |
| **venv** | Virtual environment: a private set of Python packages for one project |
| **X / y** | Conventional names for the features table (X) and the target column (y) |
| **Zero-shot** | A model that handles a task without being trained on examples of that task |
